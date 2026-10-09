# Mido 2026 — Documentación técnica

<p align="center">
    <a href="TECHNICAL.md">English</a> · <a href="TECHNICAL.es.md">Español</a> ·
    <a href="../README.es.md">README</a>
</p>

Este documento explica cómo funciona `Mido.sh` internamente: los endpoints de
Microsoft con los que habla, el handshake antiabuso, el motor de descarga, la
verificación de integridad, el soporte de idiomas y las convenciones de shell
utilizadas. Está dirigido a mantenedores y a cualquiera que quiera auditar el
script.

## Índice

1. [Requisitos](#requisitos)
2. [Estructura del repositorio](#estructura-del-repositorio)
3. [Flujo general](#flujo-general)
4. [Fuentes de datos](#fuentes-de-datos)
5. [La API JSON de consumidor](#la-api-json-de-consumidor)
6. [El handshake de Sentinel](#el-handshake-de-sentinel)
7. [Soporte de idiomas](#soporte-de-idiomas)
8. [Motor de descarga (`scurl_file`)](#motor-de-descarga-scurl_file)
9. [Verificación de integridad](#verificación-de-integridad)
10. [Manejo de errores y códigos de salida](#manejo-de-errores-y-códigos-de-salida)
11. [Constantes y configuración](#constantes-y-configuración)
12. [Propiedades de seguridad](#propiedades-de-seguridad)
13. [Solución de problemas](#solución-de-problemas)
14. [Pruebas](#pruebas)
15. [Guía de mantenimiento](#guía-de-mantenimiento)

## Requisitos

- Un shell POSIX (`sh`; en Debian/Ubuntu se prefiere `dash` y el script se
  reejecuta bajo Dash cuando está disponible).
- `curl` (con soporte de TLS 1.3 para la mejor experiencia).
- Coreutils: `grep`, `sed`, `tr`, `cut`, `head`, `tail`, `sha256sum`, `mv`,
  `dirname`, `fold`, `kill`, `date`.
- Opcional: `uuidgen` en sistemas sin `/proc/sys/kernel/random/uuid`.

## Estructura del repositorio

```
.
├── Mido.sh               # todo el programa
├── Mido.bat              # wrapper por lotes de Windows (ejecuta Mido.sh vía WSL)
├── Mido.ps1              # wrapper PowerShell de Windows (ejecuta Mido.sh vía WSL)
├── README.md             # documentación de usuario (inglés)
├── README.es.md          # documentación de usuario (español)
├── docs/
│   ├── TECHNICAL.md      # documentación técnica (inglés)
│   └── TECHNICAL.es.md   # este archivo
├── assets/               # logo, GIF de demo, capturas
└── LICENSE               # MIT
```

## Flujo general

```
parse_args "$@"
   └─ valida nombres de medios, expande "all", elimina duplicados
download_media
   └─ para cada medio: llama a la función de descarga correspondiente
verify_media
   └─ verifica cada archivo *.UNVERIFIED y lo renombra al nombre final
ending_summary
   └─ informa fallos y elige el código de salida del proceso
```

`set -ef` se activa justo antes de `parse_args`, de modo que cualquier fallo de
comando no controlado aborta el script y el trap `EXIT` (`handle_exit`) avisa de
archivos parciales. `IFS` se fija a un solo espacio, por lo que los nombres de
ISO no pueden contener espacios.

## Fuentes de datos

Las distintas familias de medios se obtienen de formas diferentes:

| Familia | Fuente | Autenticación |
|---|---|---|
| `win10x64`, `win11x64` | API JSON de consumidor | Handshake de Sentinel |
| `win10x86` (x86 español) | instantáneas de `archive.org` | ninguna |
| `win81x64` | API JSON de consumidor | Retirada (HTTP 404) |
| `win81x64-ent-32-espa`, `win81x64-ent-64-esp` | instantáneas de `archive.org` | ninguna |
| `win2008r2-espa` | instantánea de `archive.org` de la ISO de evaluación oficial en español | ninguna |
| `win10x64-enterprise-eval`, `win11x64-enterprise-eval`, `win10x64-enterprise-ltsc-eval`, `win11x64-enterprise-ltsc-eval` | instantáneas de `archive.org` | ninguna |
| `win2012r2-eval` … `win2025-eval` | HTML del Evaluation Center → redirección `go.microsoft.com/fwlink` | ninguna |
| `win81x64-enterprise-eval`, `win2008r2` | URL directa de `download.microsoft.com` | ninguna |
| `win7x64-ultimate` | instantánea de `web.archive.org` | ninguna |
| `vista_x64_sp2`, `vista_x86_sp2`, `vista_es_x64_sp2`, `vista_es_x86_sp2` | instantáneas de `archive.org` | ninguna |

> Las evaluaciones Enterprise y LTSC de Windows 10/11 se migraron desde el HTML
> (poco fiable) del Evaluation Center a ISOs archivadas en `archive.org`. La ruta
> `enterprise_eval_download` restante ahora solo la usan las evaluaciones Server
> (`win2012r2-eval` … `win2025-eval`).

## La API JSON de consumidor

Microsoft retiró el antiguo endpoint HTML
(`/software-download/contentinclude/html`), que ahora devuelve `404`. La API
actual está en:

```
https://www.microsoft.com/software-download-connector/api
```

Se usan dos endpoints:

1. `getskuinformationbyproductedition`
   `?profile=<PROFILE_ID>&productEditionId=<id>&SKU=undefined&friendlyFileName=undefined&Locale=<locale>&sessionID=<sid>`
   Devuelve la lista de SKUs (uno por idioma) de una edición de producto. Ejemplo:

   ```json
   {"Skus":[
     {"Id":"27160","Language":"English"},
     {"Id":"27129","Language":"Spanish"},
     {"Id":"27130","Language":"Spanish (Mexico)"}
   ]}
   ```

2. `GetProductDownloadLinksBySku`
   `?profile=<PROFILE_ID>&productEditionId=undefined&SKU=<sku>&friendlyFileName=undefined&Locale=<locale>&sessionID=<sid>`
   Devuelve URIs de descarga frescas, una por arquitectura:

   ```json
   {"ProductDownloadOptions":[
     {"DownloadType":2,"Uri":"https://...arm64.iso?t=..."},
     {"DownloadType":1,"Uri":"https://...x64.iso?t=..."}
   ]}
   ```

   `DownloadType` `1` es x64 (`2` es arm64). Las URIs vienen codificadas y `&`
   puede aparecer como `\u0026` o `&amp;`, por lo que se decodifican antes de
   usarlas. Los enlaces son válidos **24 horas**.

El `productEditionId` se extrae de la página de descarga visible para el usuario
(`https://www.microsoft.com/<locale>/software-download/windows<N>[ISO]`)
buscando:

```
<option value="[0-9]+">Windows
```

Por eso Mido siempre obtiene la *última* versión sin una tabla fija de releases
(a diferencia de Fido, que mantiene una).

## El handshake de Sentinel

`GetProductDownloadLinksBySku` está protegido por el servicio antiabuso de
Microsoft (*Sentinel*). Sin el handshake, la respuesta contiene:

```json
{"Errors":[{"Key":"ErrorSettings.SentinelReject",
            "Value":"Sentinel marked this request as rejected.","Type":8}]}
```

Mido ejecuta la misma secuencia que el proyecto Fido:

1. Genera un `session_id` aleatorio (`/proc/sys/kernel/random/uuid` o `uuidgen`).
2. `GET https://vlscppe.microsoft.com/tags?org_id=<ORG_ID>&session_id=<sid>`
   para poner en lista blanca la sesión.
3. `GET https://ov-df.microsoft.com/mdt.js?instanceId=<INSTANCE_ID>&PageId=si&session_id=<sid>`
   y extrae los valores `w` y `rticks`.
4. `GET https://ov-df.microsoft.com/?session_id=<sid>&CustomerId=<INSTANCE_ID>&PageId=si&w=<w>&mdt=<epoch_ms>&rticks=<rticks>`.

**Importante:** Sentinel es una comprobación *del lado del servidor basada en la
reputación de la IP*. No es un fallo ni un bug de Mido. Incluso el autor de Fido
documenta que algunos rangos de IP son rechazados y otros no. Por eso Mido
muestra un mensaje útil en lugar de un error crudo. No hay solución del lado del
cliente; las opciones son esperar (24–48 h), usar otra red/VPN o descargar
manualmente.

## Soporte de idiomas

`MIDO_LANG` (por defecto `en-US`) acepta `en-US`, `es-ES` y `es-MX`. Se mapea al
nombre de idioma que usa la API mediante `locale_to_api_language`:

| `MIDO_LANG` | Idioma para la API |
|---|---|
| `en-US` | `English` |
| `es-ES` | `Spanish` |
| `es-MX` | `Spanish (Mexico)` |

La búsqueda del SKU usa la coma final en `"Language":"<nombre>",` para que
`Spanish` no coincida por error con `Spanish (Mexico)`.

Los nombres de archivo de idiomas no por defecto llevan un sufijo de locale vía
`localized_media` (`win11x64.iso` → `win11x64.es-MX.iso`) para que las descargas
en distintos idiomas nunca se sobrescriban. Como Microsoft solo publica checksums
de las ISOs en inglés, los archivos localizados entran en la ruta
`NO KNOWN CHECKSUM (skipping verification)` (ver más abajo).

La API JSON de consumidor expone SKUs localizados solo para ediciones de consumidor
x64. Para ISOs en español de 32 bits (x86), Mido usa instantáneas de archive.org
de compilaciones Windows 10 22H2 (`win10x86-esp` para España, `win10x86-es-mx`
para México). Las ISOs en español de Windows 8.1 Enterprise también están
archivadas en archive.org (`win81x64-ent-32-espa`, `win81x64-ent-64-esp`). Una
ISO de evaluación en español de Windows Server 2008 R2 SP1 (`win2008r2-espa`)
también está archivada allí. Estas no están cubiertas por la API de Microsoft y no
tienen checksums publicados.

> Solo las ediciones de **consumidor** exponen SKUs localizados a través de esta
> API. Los medios Evaluation Enterprise/Server son únicamente en inglés, tal como
> los publica Microsoft, por lo que `enterprise_eval_download` se mantiene a
> propósito en `en-US`.

## Motor de descarga (`scurl_file`)

Todos los medios acaban llamando a `scurl_file <archivo_salida> <versión_tls> <url>`:

- `--progress-bar` — barra de progreso simple.
- `--location` — sigue redirecciones (Microsoft mueve los endpoints a menudo).
- `--output <archivo>.PART` — descarga a un archivo parcial temporal.
- `--continue-at -` — reanuda descargas parciales automáticamente.
- `--max-filesize 10G` — rechaza respuestas absurdas.
- `--fail` — falla ante HTTP ≥ 400.
- `--proto =https` — solo HTTPS.
- `--tlsv<versión>` — fuerza TLS 1.2 o 1.3.
- `--http1.1` — evita bugs conocidos de curl en HTTP/2 y HTTP/3.
- `--retry 5 --retry-delay 5 --retry-connrefused` — recuperación de fallos
  transitorios de red.
- `--speed-limit 1024 --speed-time 30` — aborta una descarga que se queda por
  debajo de 1 KiB/s durante 30 s (útil para la fuente inestable de Wayback).

Si falla, el archivo `.PART` se conserva para reanudar y se llama a
`handle_curl_error`. Las descargas correctas se renombran a `<archivo>.UNVERIFIED`,
a la espera de verificación.

## Verificación de integridad

`verify_media` lee una tabla `sha256sums` incrustada (nombre de medio → SHA-256).
Para cada medio solicitado que haya producido un archivo `.UNVERIFIED`:

1. Calcula el SHA-256 del archivo y comprueba que tenga 64 caracteres
   hexadecimales.
2. Busca el checksum esperado para ese nombre de archivo exacto.
3. Si no hay checksum registrado (normalmente una ISO localizada), imprime
   `NO KNOWN CHECKSUM (skipping verification)` y renombra al nombre final.
4. Si el checksum coincide, imprime `OK` y renombra al nombre final.
5. Si no coincide, deja `<archivo>.UNVERIFIED` y registra un fallo.

Los checksums de las versiones de consumidor **no se actualizan automáticamente**
cuando Microsoft publica una nueva compilación; una discrepancia puede significar
una versión más nueva, corrupción o manipulación. Cuando ocurre, Mido imprime
instrucciones de verificación manual (buscar el hash en DuckDuckGo/onion). Es un
compromiso deliberado para que el script no requiera mantenimiento.

El checksum de Windows 7 es inmutable: esa ISO fue purgada de los servidores de
Microsoft y la copia de Internet Archive se valida contra el checksum previo a
la purga.

## Manejo de errores y códigos de salida

`handle_curl_error` mapea los códigos de salida de `curl` a mensajes legibles y
devuelve una *acción de error fatal* (`2`) para condiciones que deben abortar
toda la ejecución (p. ej. falta de memoria). El patrón numérico por defecto
coincide intencionadamente con el rango POSIX `1–125`:

```sh
[0-9] | [0-9][0-9] | 1[0-1][0-9] | 12[0-5])
```

(Una versión anterior usaba una expansión aritmética dentro del patrón `case`
que solo coincidía con `error_code=1`; esto ya está corregido.)

Códigos de salida del proceso (ver `ending_summary`):

| Código | Significado |
|---|---|
| 0 | Éxito |
| 1 | Error de parseo de argumentos / configuración |
| 2 | Error fatal en tiempo de ejecución |
| 3 | Una o más descargas fallaron |
| 4 | Una o más verificaciones fallaron |
| 5 | Falló al menos una descarga **y** una verificación |

El trap `EXIT` (`handle_exit`) se ejecuta al interrumpir y avisa de que pueden
quedar archivos `.PART` o `.UNVERIFIED`.

## Constantes y configuración

| Variable | Por defecto | Propósito |
|---|---|---|
| `MIDO_LANG` | `en-US` | Idioma de descarga |
| `ORG_ID` | `y6jn8c31` | org id de Sentinel |
| `PROFILE_ID` | `606624d44113` | profile id de la API de consumidor |
| `INSTANCE_ID` | `560dc9f3-1aa5-4a2f-b63c-9e18f8d0e175` | instance id de `ov-df` |
| `API_BASE` | `.../software-download-connector/api` | base de la API de consumidor |
| `DEBUG` | sin definir | traza `set -x` |
| `VERBOSE` | sin definir | Imprime edition/SKU ids |

Estos valores están copiados del proyecto Fido y pueden necesitar actualizarse si
Microsoft los rota.

## Propiedades de seguridad

- Sin navegador ni motor de JavaScript: elimina una enorme superficie de ataque
  frente a manejar un navegador headless.
- Solo HTTPS (`--proto =https`) con la versión de TLS más alta disponible.
- `--http1.1` evita fallos en las implementaciones HTTP/2 y HTTP/3 de curl.
- Solo se usan builtins del shell para la lógica más crítica; los programas de
  apoyo se usan con argumentos fijos y validados.
- Toda salida no confiable del servidor se filtra (`tr -cd '[:alnum:]...'`, con
  límites de longitud vía `head -c`) antes de usarse en URLs o nombres de
  archivo, mitigando la inyección de parámetros HTTP.
- Verificación SHA-256 de cada ISO descargada.
- El argumento `all` puede combinarse con otros sin abortar el bucle, y los
  duplicados se eliminan, de modo que una sola ejecución no puede descargar dos
  veces la misma ISO grande por accidente.

## Solución de problemas

| Síntoma | Causa | Acción |
|---|---|---|
| `Sentinel marked this request as rejected` | Bloqueo por reputación de IP | Espera 24–48 h, usa una VPN o descarga manualmente |
| `715-123130` | IP baneada por Microsoft | Igual que arriba |
| `win81x64` → HTTP 404 | Microsoft retiró la automatización de Windows 8.1 | Usa `win81x64-enterprise-eval` |
| Windows 7 muy lento | Límite de velocidad de Wayback Machine | Deja actuar a `--retry`/`--continue-at`; ten paciencia |
| Windows 10 x86 en español muy lento | Límite de velocidad de `archive.org` | Deja actuar a `--retry`/`--continue-at`; ten paciencia |
| Windows 8.1 Enterprise en español muy lento | Límite de velocidad de `archive.org` | Deja actuar a `--retry`/`--continue-at`; ten paciencia |
| Windows Server 2008 R2 en español muy lento | Límite de velocidad de `archive.org` | Deja actuar a `--retry`/`--continue-at`; ten paciencia |
| Windows Vista ISOs muy lentas | Límite de velocidad de `archive.org` | Deja actuar a `--retry`/`--continue-at`; ten paciencia |
| `NO KNOWN CHECKSUM` | ISO localizada sin hash publicado | Verifica manualmente si lo deseas |
| Enterprise/Server `no download link` | La página del Evaluation Center cambió | Abre un issue |

## Pruebas

```sh
sh -n Mido.sh                 # comprobación de sintaxis POSIX
shellcheck -s sh Mido.sh      # análisis estático
```

Comprobaciones funcionales que no requieren una descarga correcta:

```sh
./Mido.sh --help
MIDO_LANG=fr-FR ./Mido.sh win11x64    # rechazado con salida 1
./Mido.sh win81x64                    # imprime el mensaje de "retirado", salida 3
VERBOSE=1 MIDO_LANG=es-MX ./Mido.sh win10x64   # muestra edition id + SKU es-MX
```

## Guía de mantenimiento

**Actualizar un checksum.** Edita el heredoc `sha256sums` en `verify_media`
después de verificar el nuevo hash por un canal independiente.

**Añadir un idioma.** Agrega el locale al `case` de validación de `MIDO_LANG` y
una rama a `locale_to_api_language`; `localized_media` funciona automáticamente.

**Añadir un medio.** Agrega una variable `readonly` junto a los demás medios, una
rama en `parse_args` (incluida la lista de `all`), un `case` en `download_media`,
una entrada en `usage()` y, si procede, una línea de checksum.

**Renovar las constantes de Sentinel.** Actualiza
`ORG_ID`/`PROFILE_ID`/`INSTANCE_ID` desde una versión actual de Fido si Microsoft
los rota.

## Licencia

MIT License — Copyright (C) 2024 Elliot Killick <contact@elliotkillick.com>.
