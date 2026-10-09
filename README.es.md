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
winget install -e --id MSYS2.MYS2
```

Ambos son entornos de emulación POSIX para Windows y puedes usar cualquiera.

#### Wrappers nativos para Windows

Mido incluye wrappers nativos para Windows para mayor comodidad:

- **`Mido.bat`** - Wrapper de archivo por lotes (ejecutar con doble clic o desde cmd)
- **`Mido.ps1`** - Wrapper de PowerShell (ejecutar desde PowerShell)

Ambos wrappers detectan automáticamente WSL y ejecutan `Mido.sh` a través de él,
pasando todos los argumentos. Ejemplos de uso:

```
Mido.bat win11x64
Mido.ps1 win10x64 win11x64
```

Si WSL no está instalado, los wrappers te pedirán que lo instales.

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
| `vista-x64-sp1` | Windows Vista SP1 x64 (inglés, obtenido de archive.org) |
| `vista-x86-sp1` | Windows Vista SP1 x86 (inglés, obtenido de archive.org) |
| `vista-es-x64-sp1` | Windows Vista SP1 x64 español (obtenido de archive.org) |
| `vista-es-x86-sp1` | Windows Vista SP1 x86 español (obtenido de archive.org) |
| `vista-x64-sp2` | Windows Vista SP2 x64 (inglés, obtenido de archive.org) |
| `vista-x86-sp2` | Windows Vista SP2 x86 (inglés, obtenido de archive.org) |
| `vista-es-x64-sp2` | Windows Vista SP2 x64 español (obtenido de archive.org) |
| `vista-es-x86-sp2` | Windows Vista SP2 x86 español (obtenido de archive.org) |
| `win7x64-ultimate` | Windows 7 Ultimate x64 (obtenido de Wayback Machine) |
| `win7x64-ultimate-esp` | Windows 7 Ultimate x64 en español — primero Microsoft oficial, si falla archive.org |
| `win7x64-ultimate-es-mx` | Windows 7 Ultimate x64 en español mexicano — primero Microsoft oficial, si falla archive.org |
| `win7x86-ultimate` | Windows 7 Ultimate x86 (32-bit) — sourced from archive.org |
| `win7x86-ultimate-esp` | Windows 7 Ultimate x86 Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win7x86-ultimate-es-mx` | Windows 7 Ultimate x86 Mexican Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win81x64` | Windows 8.1 x64 (**retirado por Microsoft**, usa la Enterprise Eval) |
| `win81x64-ent-32-espa` | Windows 8.1 Enterprise 32 bits español (obtenido de archive.org) |
| `win81x64-ent-64-esp` | Windows 8.1 Enterprise 64 bits español (obtenido de archive.org) |
| `win10x64` | Windows 10 x64 (multiedición) |
| `win10x64-esp` | Windows 10 x64 español (España) — primero Microsoft oficial, si falla archive.org |
| `win10x64-es-mx` | Windows 10 x64 español (México) — primero Microsoft oficial, si falla archive.org |
| `win10x86` | Windows 10 x86 (32-bit) español — España (`win10x86-esp`) o México (`win10x86-es-mx`) |
| `win11x64` | Windows 11 x64 (multiedición) |
| `win11x64-esp` | Windows 11 x64 español (España) — primero Microsoft oficial, si falla archive.org |
| `win11x64-es-mx` | Windows 11 x64 español (México) — primero Microsoft oficial, si falla archive.org |
| `win81x64-enterprise-eval` | Windows 8.1 Enterprise Evaluation |
| `win10x64-enterprise-eval` | Windows 10 Enterprise Evaluation |
| `win11x64-enterprise-eval` | Windows 11 Enterprise Evaluation |
| `win10x64-enterprise-ltsc-eval` | Windows 10 Enterprise LTSC Evaluation (la más segura) |
| `win11x64-enterprise-ltsc-eval` | Windows 11 Enterprise LTSC Evaluation (la más segura) |
| `win11x64-iot-enterprise-ltsc-eval` | Windows 11 IoT Enterprise LTSC Evaluation |
| `win2008r2` | Windows Server 2008 R2 SP1 (x64, Inglés) |
| `win2008r2-espa` | Windows Server 2008 R2 SP1 (x64, eval en español, obtenido de archive.org) |
| `win2008-server-x64` | Windows Server 2008 SP2 (x64, Inglés, todas las ediciones, obtenido de archive.org) |
| `win2008-server-x64-espa` | Windows Server 2008 SP2 (x64, Español, todas las ediciones, obtenido de archive.org) |
| `win2008-server-x86` | Windows Server 2008 SP2 (x86, Inglés, todas las ediciones, obtenido de archive.org) |
| `win2008-server-x86-espa` | Windows Server 2008 SP2 (x86, Español, todas las ediciones, obtenido de archive.org) |
| `win2000-pro` | Windows 2000 Professional SP4 (Inglés, obtenido de archive.org) |
| `win2000-pro-espa` | Windows 2000 Professional SP4 en Español (obtenido de archive.org) |
| `win-xp-pro` | Windows XP Professional SP3 (Inglés, obtenido de archive.org) |
| `win-xp-pro-32` | Windows XP Professional x86 (32-bit, sourced from archive.org) |
| `win-xp-pro-64` | Windows XP Professional x64 (64-bit, sourced from archive.org) |
| `win7x64-pro` | Windows 7 Professional x64 (sourced from archive.org) |
| `win7x64-pro-esp` | Windows 7 Professional x64 en español — primero Microsoft oficial, si falla archive.org |
| `win7x64-pro-es-mx` | Windows 7 Professional x64 en español mexicano — primero Microsoft oficial, si falla archive.org |
| `win7x86-pro` | Windows 7 Professional x86 (32-bit) — sourced from archive.org |
| `win7x86-pro-esp` | Windows 7 Professional x86 Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win7x86-pro-es-mx` | Windows 7 Professional x86 Mexican Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win7x64-homepremium` | Windows 7 Home Premium x64 (sourced from archive.org) |
| `win7x64-homepremium-esp` | Windows 7 Home Premium x64 en español — primero Microsoft oficial, si falla archive.org |
| `win7x64-homepremium-es-mx` | Windows 7 Home Premium x64 en español mexicano — primero Microsoft oficial, si falla archive.org |
| `win7x86-homepremium` | Windows 7 Home Premium x86 (32-bit) — sourced from archive.org |
| `win7x86-homepremium-esp` | Windows 7 Home Premium x86 Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win7x86-homepremium-es-mx` | Windows 7 Home Premium x86 Mexican Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win7x64-enterprise` | Windows 7 Enterprise x64 (sourced from archive.org) |
| `win7x64-enterprise-esp` | Windows 7 Enterprise x64 en español — primero Microsoft oficial, si falla archive.org |
| `win7x64-enterprise-es-mx` | Windows 7 Enterprise x64 en español mexicano — primero Microsoft oficial, si falla archive.org |
| `win7x86-enterprise` | Windows 7 Enterprise x86 (32-bit) — sourced from archive.org |
| `win7x86-enterprise-esp` | Windows 7 Enterprise x86 Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win7x86-enterprise-es-mx` | Windows 7 Enterprise x86 Mexican Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win7x64-sp1` | Windows 7 SP1 x64 (sourced from archive.org) |
| `win7x86-sp1` | Windows 7 SP1 x86 (32-bit, sourced from archive.org) |
| `win81x64-pro` | Windows 8.1 Pro x64 — Microsoft retiró la automatización, usa archive.org |
| `win81x64-pro-esp` | Windows 8.1 Pro en español (España) — primero Microsoft oficial, si falla archive.org |
| `win81x64-pro-es-mx` | Windows 8.1 Pro en español (México) — primero Microsoft oficial, si falla archive.org |
| `win81x86-pro` | Windows 8.1 Pro x86 (32-bit) — sourced from archive.org |
| `win81x86-pro-esp` | Windows 8.1 Pro x86 Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win81x86-pro-es-mx` | Windows 8.1 Pro x86 Mexican Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win2003-server` | Windows Server 2003 Enterprise (x86, Inglés, obtenido de archive.org) |
| `win2003-server-espa` | Windows Server 2003 Enterprise (x86, Español, obtenido de archive.org) |
| `win2003-server-x64` | Windows Server 2003 R2 Enterprise x64 SP2 (Inglés, obtenido de archive.org) |
| `win2003-server-x64-espa` | Windows Server 2003 R2 Enterprise x64 SP2 (Español, obtenido de archive.org) |
| `win2000-server` | Windows 2000 Server SP4 (Inglés, obtenido de archive.org) |
| `win2000-server-espa` | Windows 2000 Server SP4 (Español, obtenido de archive.org) |
| `win2012r2-eval` | Windows Server 2012 R2 Evaluation |
| `win2016-eval` | Windows Server 2016 Evaluation |
| `win2019-eval` | Windows Server 2019 Evaluation |
| `win2022-eval` | Windows Server 2022 Evaluation |
| `win2025-eval` | Windows Server 2025 Evaluation |
| `win2025-enterprise-64` | Windows Server 2025 Enterprise x64 (64-bit) |
| `win2025-enterprise-64-esp` | Windows Server 2025 Enterprise x64 español (64-bit) |
| `win2025-datacenter-64` | Windows Server 2025 Datacenter x64 (64-bit) |
| `win2025-datacenter-64-esp` | Windows Server 2025 Datacenter x64 español (64-bit) |
| `win2022-eval` | Windows Server 2022 Evaluation |
| `win2022-enterprise-64` | Windows Server 2022 Enterprise x64 (64-bit) |
| `win2022-enterprise-64-esp` | Windows Server 2022 Enterprise x64 español (64-bit) |
| `win2022-datacenter-64` | Windows Server 2022 Datacenter x64 (64-bit) |
| `win2022-datacenter-64-esp` | Windows Server 2022 Datacenter x64 español (64-bit) |
| `win2012r2-essentials-eval` | Windows Server 2012 R2 Essentials Evaluation |
| `win2016-essentials-eval` | Windows Server 2016 Essentials Evaluation |
| `win2019-essentials-eval` | Windows Server 2019 Essentials Evaluation |
| `hyperv2012-eval` | Hyper-V Server 2012 Evaluation |
| `hyperv2012r2-eval` | Hyper-V Server 2012 R2 Evaluation |
| `hyperv2016-eval` | Hyper-V Server 2016 Evaluation |
| `hyperv2019-eval` | Hyper-V Server 2019 Evaluation |

## Soporte de idiomas

Define la variable de entorno `MIDO_LANG` con uno de estos valores:

| Valor | Idioma |
|---|---|
| `en-US` | Inglés (Estados Unidos) — por defecto |
| `es-ES` | Español (España) |
| `es-MX` | Español (México) |

Las ISOs en español están disponibles para las versiones de **consumidor**
(`win10x64`, `win11x64`). Además de la variable `MIDO_LANG`, existen argumentos
dedicados para español de España y de México: `win10x64-esp`,
`win10x64-es-mx`, `win11x64-esp` y `win11x64-es-mx`. Estos intentan primero la
descarga oficial de Microsoft y, si la petición es rechazada (por ejemplo por el
sistema antiabuso Sentinel), usan una copia idéntica de archive.org como
respaldo. Windows 10 de 32 bits (x86) en español está
disponible vía archive.org para España (`win10x86-esp`) y México (`win10x86-es-mx`).
Windows 8.1 Enterprise en español también está disponible vía archive.org tanto
para 32 bits (`win81x64-ent-32-espa`) como para 64 bits (`win81x64-ent-64-esp`).
Una ISO de evaluación en español de Windows Server 2008 R2 SP1 está disponible
vía archive.org (`win2008r2-espa`). Windows Server 2008 (sin R2) SP2 está
disponible vía archive.org en inglés y español, tanto para 64 bits
(`win2008-server-x64`, `win2008-server-x64-espa`) como para 32 bits
(`win2008-server-x86`, `win2008-server-x86-espa`). Windows Server 2003 Enterprise y Windows 2000
Server SP4 también están disponibles en inglés y español vía archive.org
(`win2003-server`, `win2003-server-espa`, `win2000-server`, `win2000-server-espa`).
Windows Server 2003 R2 Enterprise x64 SP2 de 64 bits está disponible en inglés
(`win2003-server-x64`) y español (`win2003-server-x64-espa`).
Windows Vista SP1 en español también está disponible vía archive.org
(`vista-es-x64-sp1`, `vista-es-x86-sp1`).
Windows Vista SP2 en español también está disponible vía archive.org
(`vista-es-x64-sp2`, `vista-es-x86-sp2`).
Los medios Enterprise, Server y Evaluation son, por lo demás, únicamente en inglés,
tal como los publica Microsoft. Las ISOs en otros idiomas se guardan con un sufijo
de idioma (por ejemplo `win11x64.es-MX.iso`) o con un nombre `-espa`/`-esp`/
`-es-mexico` según corresponda, para no sobrescribir las inglesas.

Windows 2000 Professional, Windows XP Professional y Windows XP Professional x64
 en español también están disponibles vía archive.org. Windows 8.1 Pro en español
 también está disponible vía archive.org tanto para 64-bit (`win81x64-pro-esp`)
 como para 32-bit (`win81x64-pro-es-mx`). Como Microsoft no publica checksums
 oficiales de cada versión localizada, las ISOs localizadas pueden mostrar
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
automáticamente las últimas ediciones Server (por ejemplo Windows Server 2025) y
Enterprise de casi todas las versiones, desde Windows 7 (o Server 2008 R2, e
incluso Server 2003 y 2000 Server) en adelante.

¿Quieres una instalación de Windows más segura y minimalista, pero oficial de
Microsoft? Descarga la versión LTSC de Windows. Incluye mucho menos bloatware y
soporta el modo de telemetría ["Security"](https://learn.microsoft.com/es-es/windows/privacy/configure-windows-diagnostic-data-in-your-organization#diagnostic-data-settings)
de Microsoft (además de soporte a largo plazo).

## Base de conocimiento / solución de problemas

- **"Sentinel marked this request as rejected"** — el sistema antiabuso de
  Microsoft bloqueó la petición según la reputación de tu IP. Espera 24–48 horas,
  usa una VPN o descarga manualmente desde la página oficial. **No** es un bug de
  Mido; el autor de Fido documenta el mismo comportamiento. Los argumentos
  `win10x64-esp`, `win10x64-es-mx`, `win11x64-esp` y `win11x64-es-mx` evitan este
  problema porque recurren automáticamente a una copia de archive.org.
- **Respaldo archive.org** — las ISOs en español dedicadas intentan primero la
  descarga oficial de Microsoft y, si Sentinel la rechaza, descargan una copia
  idéntica desde `archive.org` (más lento que la CDN de Microsoft). Mido reintenta
  y reanuda automáticamente.
- **Windows 8.1 (`win81x64`) falla con HTTP 404** — Microsoft retiró la descarga
  automática de Windows 8.1. Usa `win81x64-enterprise-eval`.
- **Windows 7 va lento** — proviene de `web.archive.org`, mucho más lento y menos
  fiable que la CDN de Microsoft. Mido reintenta y reanuda automáticamente.
- **Windows 10 x86 en español** — proviene de `archive.org` (compilaciones Windows
  10 22H2), que puede ser más lento que la CDN de Microsoft. Mido reintenta y
  reanuda automáticamente.
- **Windows 8.1 Enterprise en español** — proviene de `archive.org`, que puede ser
  más lento que la CDN de Microsoft. Mido reintenta y reanuda automáticamente.
- **Windows Server 2008 R2 en español** — proviene de `archive.org`, que puede ser
  más lento que la CDN de Microsoft. Mido reintenta y reanuda automáticamente.
- **Windows Server 2003 / Windows 2000 Server** — provienen de `archive.org`, que
  puede ser más lento que la CDN de Microsoft. Mido reintenta y reanuda
  automáticamente.
- **Windows Vista en español** — proviene de `archive.org`, que puede ser más
  lento que la CDN de Microsoft. Mido reintenta y reanuda automáticamente.
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
