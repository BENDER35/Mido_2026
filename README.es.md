<div align="center">
    <a href="https://github.com/BENDER35/Mido_2026">
        <img width="160" src="assets/logo.png" alt="Logo" />
    </a>
</div>

<h3 align="center">
    Mido 2026
</h3>

<p align="center">
    El descargador <b>seguro</b> de Microsoft Windows
</p>

<p align="center">
    <a href="README.md">English</a> · <a href="README.es.md">Español</a>
</p>

¡Mido es un cliente de descarga libre y seguro para la API de descargas
(reverse-engineered) de Microsoft! Las descargas provienen de servidores
**oficiales** de Microsoft y solo necesitas ejecutar un comando para ir de
principio a fin.

Incluye funciones avanzadas como reanudación de descargas, verificación de
checksums SHA-256, lógica de reintentos, ISOs multiidioma (inglés, español y
español de México) y la descarga de varias versiones de Windows en un solo
comando. Está escrito en *POSIX sh puro* (con pocos coreutils) + curl, por lo que
funciona en cualquier sitio (incluso en Windows con WSL o Cygwin).

> **Actualización 2026:** Microsoft reemplazó su antigua API HTML de descargas
> por una API JSON (`software-download-connector`) protegida por un servicio
> antiabuso (Microsoft Sentinel). Mido 2026 implementa la API nueva y el
> handshake necesario, corrige varios bugs de shell, añade reintentos y soporte
> de español. Consulta [docs/TECHNICAL.es.md](docs/TECHNICAL.es.md) para los detalles.

#### ❌ La herramienta de creación de medios de Microsoft (`mediacreationtool.exe`, software privativo con bloatware)

<p align="center">
    <img src="assets/bloatware1.png" alt="Ejecutable de bloatware privativo de Microsoft"></img>
    <br />
    <img src="assets/bloatware2.png" width="250px" alt="Bloatware privativo de Microsoft"></img>
    <img src="assets/bloatware3.png" width="200px" alt="Bloatware de Microsoft"></img>
</p>

Sitio web con bloatware: `https://www.microsoft.com/en-us/software-download/windows11`
- Mido ofrece exactamente las mismas descargas que este sitio web (usa la misma API)

#### ✅ Mido (usando los **mismos** servidores oficiales de Microsoft; software libre)

<p align="center">
    <img src="assets/demo.gif" width="400" alt="GIF de demostración del proyecto"></img>
</p>

## Obtener Mido

Descarga [Mido.sh](https://raw.githubusercontent.com/BENDER35/Mido_2026/main/Mido.sh) abriendo el enlace, haciendo clic derecho y seleccionando "Guardar [página] como...".

### Mac y Linux

¡Ya está! Abre una terminal, da permisos de ejecución al archivo (`chmod +x Mido.sh`) y ejecútalo.

### Windows

Para ejecutar Mido en Windows, usa WSL (Windows Subsystem for Linux). Si aún no
lo tienes habilitado, busca "Activar o desactivar características de Windows" en
el menú Inicio, abre esa opción, marca la casilla "Windows Subsystem for Linux" y
haz clic en "Aceptar". Esta es la mejor opción.

Como alternativa, instala [Cygwin](https://www.cygwin.com/install.html) o
[MSYS2](https://www.msys2.org/#installation), o en un solo comando con WinGet:

```
winget install -e --id Cygwin.Cygwin
winget install -e --id MSYS2.MSYS2
```

Ambos son entornos de emulación POSIX para Windows y puedes usar cualquiera.

## Uso

```
./Mido.sh <windows_media>...
```

Ejemplos:

```
./Mido.sh win11x64                       # Windows 11, inglés (EE. UU.)
MIDO_LANG=es-ES ./Mido.sh win10x64       # Windows 10, español (España)
MIDO_LANG=es-MX ./Mido.sh win11x64       # Windows 11, español (México)
./Mido.sh win7x64-ultimate win10x64      # Varias ISOs en un solo comando
./Mido.sh all                            # Todos los medios soportados
```

### Medios disponibles

| Argumento | Descripción |
|---|---|
| `win7x64-ultimate` | Windows 7 Ultimate x64 (obtenido de Wayback Machine) |
| `win81x64` | Windows 8.1 x64 (**retirado por Microsoft**, usa la Enterprise Eval) |
| `win10x64` | Windows 10 x64 (multiedición) |
| `win11x64` | Windows 11 x64 (multiedición) |
| `win81x64-enterprise-eval` | Windows 8.1 Enterprise Evaluation |
| `win10x64-enterprise-eval` | Windows 10 Enterprise Evaluation |
| `win11x64-enterprise-eval` | Windows 11 Enterprise Evaluation |
| `win10x64-enterprise-ltsc-eval` | Windows 10 Enterprise LTSC Evaluation (la más segura) |
| `win2008r2` | Windows Server 2008 R2 |
| `win2012r2-eval` | Windows Server 2012 R2 Evaluation |
| `win2016-eval` | Windows Server 2016 Evaluation |
| `win2019-eval` | Windows Server 2019 Evaluation |
| `win2022-eval` | Windows Server 2022 Evaluation |

## Soporte de idiomas

Define la variable de entorno `MIDO_LANG` con uno de estos valores:

| Valor | Idioma |
|---|---|
| `en-US` | Inglés (Estados Unidos) — por defecto |
| `es-ES` | Español (España) |
| `es-MX` | Español (México) |

Las ISOs en español están disponibles para las versiones de **consumidor**
(`win10x64`, `win11x64`). Los medios Enterprise, Server y Evaluation son
únicamente en inglés, tal como los publica Microsoft. Las ISOs en otros idiomas
se guardan con un sufijo de idioma (por ejemplo `win11x64.es-MX.iso`) para no
sobrescribir las inglesas. Como Microsoft no publica checksums oficiales de cada
versión localizada, las ISOs localizadas pueden mostrar
`NO KNOWN CHECKSUM (skipping verification)`.

## ¿Cómo funciona Mido?

Interactúa con la API propietaria de descargas de Microsoft para obtener la
última versión de Windows y generar un enlace de descarga fresco (válido 24
horas). Después descarga el archivo, verifica su checksum SHA-256 y reanuda las
descargas parciales automáticamente.

En detalle:

1. Extrae de la página oficial el *product edition ID* de la versión solicitada.
2. Registra un *session ID* aleatorio y completa el handshake antiabuso de
   Microsoft (*Sentinel*).
3. Consulta la API JSON para obtener el *SKU ID* del idioma solicitado.
4. Solicita un enlace de descarga fresco y lo descarga en `<archivo>.PART`.
5. Verifica el checksum SHA-256 y renombra el archivo a `<archivo>` (OK) o
   `<archivo>.UNVERIFIED` (requiere revisión manual).

Consulta [docs/TECHNICAL.es.md](docs/TECHNICAL.es.md) para el recorrido completo.

## ¿Qué más puede hacer Mido?

Además de las versiones de consumidor (Windows 10 y 11), puede descargar
automáticamente las últimas ediciones Server (por ejemplo Windows Server 2022) y
Enterprise de casi todas las versiones, desde Windows 7 (o Server 2008 R2) en
adelante.

¿Quieres una instalación de Windows más segura y minimalista, pero oficial de
Microsoft? Descarga la versión LTSC de Windows. Incluye mucho menos bloatware y
soporta el modo de telemetría ["Security"](https://learn.microsoft.com/es-es/windows/privacy/configure-windows-diagnostic-data-in-your-organization#diagnostic-data-settings)
de Microsoft (además de soporte a largo plazo).

## Base de conocimiento / solución de problemas

- **"Sentinel marked this request as rejected"** — el sistema antiabuso de
  Microsoft bloqueó la petición según la reputación de tu IP. Espera 24–48 horas,
  usa una VPN o descarga manualmente desde la página oficial. **No** es un bug de
  Mido; el autor de Fido documenta el mismo comportamiento.
- **Windows 8.1 (`win81x64`) falla con HTTP 404** — Microsoft retiró la descarga
  automática de Windows 8.1. Usa `win81x64-enterprise-eval`.
- **Windows 7 va lento** — proviene de `web.archive.org`, mucho más lento y menos
  fiable que la CDN de Microsoft. Mido reintenta y reanuda automáticamente.
- **Enterprise/Server "no download link"** — Microsoft cambia periódicamente las
  páginas del Evaluation Center. Abre un issue con la versión afectada.

## ¿Quieres ahorrar más tiempo?

Echa un vistazo al script `create-media.sh` de
[Qvm-Create-Windows-Qube](https://github.com/ElliotKillick/qvm-create-windows-qube/tree/master/windows).

## ¿Qué tan seguro es *realmente*?

Mido es software razonablemente seguro. Se aprovecha cada oportunidad para
reducir la superficie de ataque. Los datos no confiables se tratan como tales
con pasos de validación adecuados. Siempre se usa la versión más alta posible de
TLS (hasta TLS 1.3).

- Sin navegador web (Chromium headless ejecutando JavaScript), lo que reduce la
  superficie de ataque en *muchos* órdenes de magnitud.
- Se fuerza TLS 1.2 o TLS 1.3 (esta última cuando los servidores de Microsoft la
  soportan).
- Compatible con POSIX sh y cambia automáticamente a un shell más seguro (Dash)
  si está disponible.
- Se fuerza HTTP/1.1 para evitar los frecuentes [bugs de curl en HTTP/2 y HTTP/3](https://github.com/curl/curl/issues?q=is%3Aissue+label%3Acrash).
- Solo se usan builtins del shell para la funcionalidad más crítica.
- Se verifica el checksum SHA-256 de cada ISO descargada.

## Pruebas

```sh
sh -n Mido.sh          # comprobación de sintaxis
shellcheck -s sh Mido.sh
```

## Licencia

MIT License - Copyright (C) 2024 Elliot Killick <contact@elliotkillick.com>
