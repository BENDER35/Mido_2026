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
| `win10x86` | Windows 10 x86 (32-bit, multiedición) — primero Microsoft oficial, si falla archive.org |
| `win10x86-esp` | Windows 10 x86 (32-bit) español (España) — primero Microsoft oficial, si falla archive.org |
| `win10x86-es-mx` | Windows 10 x86 (32-bit) español (México) — primero Microsoft oficial, si falla archive.org |
| `win11x64` | Windows 11 x64 (multiedición) |
| `win11x64-esp` | Windows 11 x64 español (España) — primero Microsoft oficial, si falla archive.org |
| `win11x64-es-mx` | Windows 11 x64 español (México) — primero Microsoft oficial, si falla archive.org |
| `win81x64-enterprise-eval` | Windows 8.1 Enterprise Evaluation |
| `win10x64-enterprise-eval` | Windows 10 Enterprise Evaluation |
| `win10x86-enterprise-eval` | Windows 10 Enterprise 32-bit Evaluation (LTSC 21H2 x86, obtenido de archive.org) |
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
| `win2000-pro-oem` | Windows 2000 Professional SP3 OEM (Inglés, obtenido de archive.org) |
| `win2000-pro-retail` | Windows 2000 Professional SP4 Retail (Inglés, obtenido de archive.org) |
| `win2000-pro-oem-espa` | Windows 2000 Professional SP1 OEM en Español (obtenido de archive.org) |
| `win2000-pro-retail-espa` | Windows 2000 Professional RTM Retail en Español (obtenido de archive.org) |
| `win-xp-pro` | Windows XP Professional SP3 (Inglés, obtenido de archive.org) |
| `win-xp-pro-espa` | Windows XP Professional SP3 en Español (obtenido de archive.org) |
| `win-xp-pro-32` | Windows XP Professional x86 (32-bit, obtenido de archive.org) |
| `win-xp-pro-32-espa` | Windows XP Professional x86 SP3 en Español (32-bit, obtenido de archive.org) |
| `win-xp-pro-64` | Windows XP Professional x64 SP2 (64-bit, Inglés, obtenido de archive.org) |
| `win-xp-home` | Windows XP Home Edition SP3 (Inglés, obtenido de archive.org) |
| `win-xp-home-espa` | Windows XP Home Edition SP3 en Español (obtenido de archive.org) |
| `win-xp-home-oem` | Windows XP Home Edition RTM OEM (Inglés, obtenido de archive.org) |
| `win-xp-home-retail` | Windows XP Home Edition SP3 Retail (Inglés, obtenido de archive.org) |
| `win-xp-home-oem-espa` | Windows XP Home Edition SP2 OEM en Español (obtenido de archive.org) |
| `win-xp-home-retail-espa` | Windows XP Home Edition SP3 Retail en Español (obtenido de archive.org) |
| `win-xp-pro-oem` | Windows XP Professional SP2 OEM (Inglés, obtenido de archive.org) |
| `win-xp-pro-retail` | Windows XP Professional SP3 Retail (Inglés, obtenido de archive.org) |
| `win-xp-pro-oem-espa` | Windows XP Professional SP2 OEM en Español (obtenido de archive.org) |
| `win-xp-pro-retail-espa` | Windows XP Professional SP1 Retail en Español (obtenido de archive.org) |
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
| `win2000-advanced-server` | Windows 2000 Advanced Server SP1 (Inglés, obtenido de archive.org) |
| `win2000-advanced-server-espa` | Windows 2000 Advanced Server SP1 (Español, obtenido de archive.org) |
| `win2000-datacenter` | Windows 2000 Datacenter Server RTM OEM (SP1, Inglés, obtenido de archive.org) |
| `win2000-datacenter-sp4` | Windows 2000 Datacenter Server SP4 (Inglés, obtenido de archive.org) |
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
| `win311` | Windows for Workgroups 3.11 (inglés, obtenido de archive.org) |
| `win95` | Windows 95 (inglés, obtenido de archive.org) |
| `win95-espa` | Windows 95 en Español (obtenido de archive.org) |
| `win98` | Windows 98 Second Edition (inglés, obtenido de archive.org) |
| `win98-espa` | Windows 98 Second Edition en Español (obtenido de archive.org) |
| `winme` | Windows Millennium Edition (inglés, obtenido de archive.org) |
| `winme-espa` | Windows Millennium Edition en Español (obtenido de archive.org) |
| `winnt31` | Windows NT 3.1 Workstation (inglés, obtenido de archive.org) |
| `winnt35` | Windows NT 3.5 Workstation (inglés, obtenido de archive.org) |
| `winnt351` | Windows NT 3.51 Workstation (inglés, obtenido de archive.org) |
| `winnt40` | Windows NT 4.0 Workstation (inglés, obtenido de archive.org) |
| `backoffice` | Microsoft BackOffice Small Business Server 4.0 (x86, obtenido de archive.org) |
| `winframe` | Citrix WinFrame 1.6 (Windows NT 3.51 Terminal Server Edition, obtenido de archive.org) |

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
respaldo. Windows 10 de 32 bits (x86) está disponible vía archive.org
para inglés (`win10x86`), España (`win10x86-esp`) y México (`win10x86-es-mx`).
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

Windows 2000 Professional, Windows XP Professional, Windows XP Home Edition y
 Windows XP Professional x64 en español también están disponibles vía archive.org. Windows 8.1 Pro en español
 también está disponible vía archive.org tanto para 64-bit (`win81x64-pro-esp`)
 como para 32-bit (`win81x64-pro-es-mx`). Como Microsoft no publica checksums
oficiales de cada versión localizada, las ISOs localizadas pueden mostrar
  `NO KNOWN CHECKSUM (skipping verification)`.

Windows 2000 Professional y Windows XP están además disponibles en los canales
de licencia originales **OEM** y **Retail** (FPP), en inglés y español:
`win2000-pro-oem`, `win2000-pro-retail`, `win2000-pro-oem-espa`,
`win2000-pro-retail-espa`, `win-xp-home-oem`, `win-xp-home-retail`,
`win-xp-home-oem-espa`, `win-xp-home-retail-espa`, `win-xp-pro-oem`,
`win-xp-pro-retail`, `win-xp-pro-oem-espa` y `win-xp-pro-retail-espa`.
Windows XP Home Edition y Windows XP Professional están disponibles en inglés y
español (`win-xp-home`, `win-xp-home-espa`, `win-xp-pro`, `win-xp-pro-espa`,
`win-xp-pro-32-espa`).

> **Nota sobre el español de México en Windows antiguos:** Windows 2000 y
> Windows XP fueron publicados por Microsoft como una **única versión en español**
> que sirve a todas las regiones hispanohablantes (no existe un ISO es-MX aparte
> para estas versiones). El español de México (`es-MX`) solo se ofrece para las
> versiones que Microsoft localizó por separado: Windows 7, Windows 8.1,
> Windows 10 y Windows 11 (argumentos `-es-mx` y `MIDO_LANG=es-MX`).

Las versiones retro (`win311`, `win95`, `win98`, `winme`, `winnt31`, `winnt35`,
`winnt351`, `winnt40`, `backoffice`, `winframe`) provienen de `archive.org` y
no tienen checksums publicados. Windows 95, Windows 98 Second Edition y Windows
Millennium Edition también están disponibles en español (`win95-espa`,
`win98-espa`, `winme-espa`); el resto de las versiones retro son únicamente en
inglés.

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
incluso Server 2003 y 2000 Server) en adelante. También incluye versiones retro
de los años 90: Windows for Workgroups 3.11, Windows 95, Windows 98 Second
Edition, Windows Millennium Edition, Windows NT 3.1, 3.5 y 3.51 Workstation y
Windows NT 4.0 Workstation (`win311`, `win95`, `win98`, `winme`, `winnt31`,
`winnt35`, `winnt351`, `winnt40`, obtenidas de archive.org). También ofrece los
productos de servidor de la era Windows NT 3.51: Microsoft BackOffice Small
Business Server 4.0 (`backoffice`) y Citrix WinFrame 1.6 (Windows NT 3.51
Terminal Server Edition, `winframe`). Windows 95, 98 y Millennium Edition
también tienen ISOs en
español (`win95-espa`, `win98-espa`, `winme-espa`), y Windows 2000 Professional
/ Windows XP están disponibles en los canales de licencia OEM y Retail
originales (consulta la tabla de medios más arriba).

¿Quieres una instalación de Windows más segura y minimalista, pero oficial de
Microsoft? Descarga la versión LTSC de Windows. Incluye mucho menos bloatware y
soporta el modo de telemetría ["Security"](https://learn.microsoft.com/es-es/windows/privacy/configure-windows-diagnostic-data-in-your-organization#diagnostic-data-settings)
de Microsoft (además de soporte a largo plazo).

## Activación de producto: se elimina la activación telefónica y se sustituye por activación por internet

Windows NT 3.1, 3.5 y 3.51 (y todos los medios de este proyecto anteriores a
Windows XP) **no tienen ningún sistema de activación de producto**. Microsoft
introdujo la activación de producto con Windows XP y Office XP en 2001, y más
tarde la usó en Windows Vista, 7, 8.1, 10, 11, la línea Windows Server y
Microsoft Office.

Microsoft ha **suprimido el antiguo sistema de activación telefónica** y lo ha
**sustituido por un sistema de activación por internet**: el **Product
Activation Portal**. A partir del **3 de diciembre de 2025** la automatización
tradicional de activación de producto por teléfono pasó del teléfono a internet.
Si llamas al antiguo número de activación ahora escucharás una grabación que te
remite al portal online. La activación sin conexión sigue siendo compatible:
solo ha cambiado la forma de obtener el *Confirmation ID* (un portal web en
lugar del sistema telefónico automático).

Pasos de la nueva activación por internet:

1. En el equipo que necesita activarse, abre el diálogo de activación de Windows
   y elige **Activar por teléfono** (o ejecuta `SLUI 04`) para mostrar el
   **Installation ID**. El equipo puede permanecer sin conexión: anota el ID.
2. Desde **cualquier otro dispositivo con acceso a internet**, abre el
   **Product Activation Portal**: <https://visualsupport.microsoft.com> (enlace
   corto: `aka.ms/aoh`).
3. Completa el CAPTCHA, selecciona **Proceed to Sign In** e inicia sesión con
   una cuenta compatible (cuenta personal de Microsoft, cuenta de trabajo o
   educativa, Microsoft Entra ID o un inquilino de Azure Government). La cuenta
   solo sirve para acceder de forma segura al portal y **no** está vinculada a
   la licencia del producto.
4. Elige **Activate a Microsoft product** e introduce el **Installation ID** del
   paso 1.
5. El portal devuelve un **Confirmation ID**.
6. Escribe el **Confirmation ID** en el diálogo de activación del equipo sin
   conexión para completar la activación.

Windows 10 Pro for Workstations y Windows 10/11 Pro Education deben activarse
por internet y no admiten el flujo sin conexión. Los clientes de licencias por
volumen pueden usar la Volume Activation Management Tool (VAMT) para la
activación proxy o KMS / activación basada en Active Directory.

## NewShell ("Shell Technology Preview") para Windows NT 3.51

El escritorio retro de Windows NT 3.1/3.5/3.51 es el antiguo shell de Program
Manager/File Manager. En 1995 Microsoft publicó una renovación del shell llamada
**NewShell** (también conocida como **Shell Technology Preview**) que porta el
shell Explorer de Windows 95 —barra de tareas, menú Inicio y escritorio— a
**Windows NT 3.51**. Es una beta cuyo único objetivo era mostrar cómo sería el
futuro Windows NT 4.0, y es un parche muy popular para dar a NT clásico un
escritorio usable.

> **Importante:** NewShell se produjo **solo para Windows NT 3.51**. **No**
> funciona en Windows NT 3.1 ni en el Windows NT 3.5 original (build 807). Sí
> existió una vista previa temprana del "shell de Chicago" con el Windows NT 3.5
> build 854 filtrado, que está archivada por separado (ver abajo).

Descargas:

- **Windows NT 3.51 NewShell / Shell Technology Preview** (las dos versiones
  públicas: 26 de mayo de 1995 y el "Shell Technology Preview Update" del 8 de
  agosto de 1995): <https://winworldpc.com/product/newshell/beta>
- **Windows NT 3.5 build 854 NewShell** (vista previa temprana, `WINSRV.DLL`
  completo): <https://archive.org/details/newshell.3.50.854>
  — descarga directa:
  <https://archive.org/download/newshell.3.50.854/NT_854_newshell.zip>

Instalación del **Shell Technology Preview de Windows NT 3.51**:

1. Instala primero Windows NT 3.51 Workstation (o Server) y, recomendado, aplica
   el último service pack de NT 3.51.
2. Descarga el archivo de NewShell y descomprímelo (contiene los archivos
   actualizados del shell Explorer).
3. Lee el `readme.wri` incluido en el archivo: indica los comandos de copia
   exactos para cada versión. En resumen, la actualización hace copia de
   seguridad del shell original, copia los nuevos archivos del shell Explorer
   (por ejemplo `shell32.dll`, `explorer.exe`, `comctl32.dll`, `shdocvw.dll`)
   sobre `%SystemRoot%\System32`, actualiza el registro y sustituye Program
   Manager / File Manager por el nuevo shell.
4. Haz una instantánea o copia de seguridad del sistema antes de aplicarlo, ya
   que NewShell es una beta sin soporte.
5. Reinicia. Deberías tener la barra de tareas y el menú Inicio estilo
   Windows 95.

Instalación del **NewShell de Windows NT 3.5 build 854**:

1. Copia los directorios `IDW`, `MSTOOLS` y `UI` de la carpeta `NEWSHELL`/`GUI`
   del medio al directorio de Windows (normalmente `\WINNT35`).
2. Abre Panel de control → **Sistema** y añade
   `%SystemRoot%\idw;%SystemRoot%\mstools;%SystemRoot%\ui` a la variable de
   entorno `Path`.
3. Reinicia.

## Windows Update en Windows sin soporte

Descargar los medios es solo la mitad: una vez instalado Windows hay que
actualizarlo. Microsoft ha ido cerrando los servicios de los que dependen las
versiones antiguas, por lo que una instalación nueva puede no encontrar
actualizaciones en absoluto.

- **Windows Vista y anteriores (incluido Windows Server 2008 y anteriores).** El
  servicio clásico de **Windows Update / Microsoft Update** está cerrado de forma
  permanente. Estos sistemas ya no pueden acceder a los servidores de
  actualizaciones de Microsoft por sí solos.
- **Windows 7 / Windows Server 2008 R2.** El **Microsoft Update para "otros
  productos de Microsoft"** (Office, etc.) está cerrado y, además, las propias
  actualizaciones de Windows solo funcionan tras instalar una serie de parches
  requisito. El proyecto comunitario *Legacy Update* restaura ambos.
- **Windows 8.1 / Windows Server 2012 R2.** Windows Update normal todavía
  funciona, pero estas versiones están sin soporte, así que solo las herramientas
  de la comunidad que siguen ofreciendo la lista completa de actualizaciones.

### Proyectos comunitarios de recuperación

Como Microsoft retiró los servicios originales, la comunidad los ha reconstruido:

- **Legacy Update** — <https://legacyupdate.net/> — la opción recomendada para
  **Windows 2000, XP, Vista, 7, 8, 8.1, 10 y 11** y las versiones equivalentes de
  Windows Server (x86, x64, Itanium y ARM64). Redirige el protocolo moderno
  Windows Update v6 a los servidores oficiales de Microsoft e instala
  automáticamente los parches requisito que faltan en instalaciones nuevas
  (compatibilidad con firma de código SHA-2 `KB4474419`/`KB4490628`, el update de
  la pila de servicio y más).
- **Windows Update Restored** — <https://windowsupdaterestored.com/> — el proyecto
  hermano que revive los sitios **originales** de Windows Update y Microsoft
  Update para **Windows 95, NT 4.0, 98, Me, 2000 y XP**. Requiere **Internet
  Explorer 4.0–6.0** (IE 5.5 a 800×600 para el aspecto clásico) y está pensado
  para esas versiones antiguas.

> Ambos proyectos son no oficiales, gestionados por la comunidad y no están
> afiliados a Microsoft. Son la única forma realista de actualizar estos sistemas
> hoy. Úsalos en una máquina que puedas permitirte reinstalar y mantén copias de
> seguridad. Incluso totalmente parcheados, estos sistemas siguen siendo un riesgo
> de seguridad: prioriza un sistema con soporte cuando sea posible.

### Windows 7: habilitar actualizaciones para "otros productos de Microsoft"

En una instalación nueva de Windows 7 suele faltar la opción **"Obtener
actualizaciones para otros productos de Microsoft"** (Microsoft Update), por lo
que Windows Update solo ofrece actualizaciones de Windows. Para restaurar el
Microsoft Update completo:

1. Instala primero los parches requisito. Lo más fácil es ejecutar **Legacy
   Update**, que detecta e instala todo lo que falta. De forma manual necesitas
   `KB4474419` (compatibilidad con firma de código SHA-2) y `KB4490628` (pila de
   servicio), seguidos de un reinicio y del último update de la pila de servicio.
2. Instala **Office 2010** (cualquier edición). Su instalación instala el agente
   **Microsoft Update** más reciente, que vuelve a registrar el origen de
   actualizaciones "otros productos de Microsoft" que necesita el Windows 7
   moderno.
3. Reinicia y, en Internet Explorer, visita <http://update.microsoft.com> y/o
   abre **Windows Update** y haz clic en **Find out more** junto a *"Get updates
   for other Microsoft products"* para aceptar. (Si la opción sigue oculta, abre
   una vez una aplicación de Office como Word y acepta el aviso de
   "actualizaciones recomendadas").
4. Ve a **Windows Update → Cambiar configuración** y confirma que ha vuelto la
   sección *Microsoft Update*.

> Si Office 2010 se instala **antes** de ejecutar Legacy Update, la búsqueda de
> actualizaciones puede quedarse colgada para siempre — es un problema conocido
> de Legacy Update. Ejecuta Legacy Update primero.

### Cómo buscar e instalar actualizaciones, paso a paso

**Windows modernos (8.1 / 10 / 11) que aún sirve Windows Update:**

1. Abre **Configuración → Actualización y seguridad → Windows Update**.
2. Pulsa **Buscar actualizaciones** y deja que descargue e instale.
3. Para actualizaciones opcionales y de controladores, pulsa **Ver actualizaciones
   opcionales** (Windows 10/11) y selecciona lo que necesites.
4. Reinicia y repite hasta que no aparezcan más actualizaciones.

**Windows antiguos (2000 – 8.1) con Legacy Update:**

1. Descarga `LegacyUpdateSetup.exe` de <https://legacyupdate.net/> y ejecútalo en
   la máquina destino (Windows 2000/XP pueden necesitar el workaround documentado
   y el último Internet Explorer primero).
2. Acepta el aviso para instalar los **parches requisito**. Legacy Update detecta
   e instala todo lo que falta y reinicia cuando es necesario.
3. Abre **Inicio → Todos los programas → Legacy Update** (o el enlace **Install
   Updates** del sitio) para cargar la página clásica de Windows Update.
4. Pulsa **Express** para instalar todas las actualizaciones críticas/de seguridad
   automáticamente, o **Custom** para elegir actualizaciones, controladores y
   software opcional.
5. Revisa la lista, pulsa **Install Updates**, acepta las licencias cuando se te
   pida y deja que instale.
6. Reinicia cuando se te pida y **vuelve a buscar hasta que no aparezcan
   actualizaciones nuevas**. La primera búsqueda en Vista/7 puede tardar 30–60
   minutos — ten paciencia.

**Windows muy antiguos (95 / 98 / Me / NT 4.0) con Windows Update Restored:**

1. En la máquina destino abre `windowsupdaterestored.com` en **Internet Explorer
   4.0–6.0**.
2. Sigue el **Prerequisites Installer** del sitio (o los pasos manuales); el sitio
   carga el control ActiveX original de Microsoft que busca actualizaciones.
3. Usa **Windows Update** para actualizaciones del sistema y **Microsoft Update**
   para Office y otros productos de Microsoft.
4. Instala las actualizaciones ofrecidas, reinicia y vuelve a buscar hasta que la
   lista quede vacía.

**Alternativa manual:** casi todas las actualizaciones también se publican en el
**Catálogo de Microsoft Update** (<https://catalog.update.microsoft.com/>). Busca
por número de KB, descarga el `.msu`/`.cab` e instálalo haciendo doble clic (o con
`wusa`/`dism`). Es la forma más fiable de parchear una máquina que no puede
acceder a ningún servicio de actualizaciones.

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
- **Windows 10 x86 (inglés, español)** — se intenta primero la descarga oficial;
  el respaldo de `archive.org` (compilaciones Windows 10 22H2) puede ser más
  lento que la CDN de Microsoft. Mido reintenta y reanuda automáticamente.
- **Windows 8.1 Enterprise en español** — proviene de `archive.org`, que puede ser
  más lento que la CDN de Microsoft. Mido reintenta y reanuda automáticamente.
- **Windows Server 2008 R2 en español** — proviene de `archive.org`, que puede ser
  más lento que la CDN de Microsoft. Mido reintenta y reanuda automáticamente.
- **Windows Server 2003 / Windows 2000 Server** — provienen de `archive.org`, que
  puede ser más lento que la CDN de Microsoft. Mido reintenta y reanuda
  automáticamente.
- **Windows Vista en español** — proviene de `archive.org`, que puede ser más
  lento que la CDN de Microsoft. Mido reintenta y reanuda automáticamente.
- **ISOs retro (`win311`, `win95`, `win98`, `winme`, `winnt31`, `winnt35`,
  `winnt351`, `winnt40`, `backoffice`, `winframe`, además de las españolas
  `win95-espa`, `win98-espa`, `winme-espa`)** — provienen de `archive.org`, que
  puede ser más lento que la CDN de Microsoft. Mido reintenta y reanuda
  automáticamente.
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
