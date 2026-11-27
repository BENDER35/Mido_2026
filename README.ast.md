<div align="center">
    <a href="https://github.com/BENDER35/Mido_2026">
        <img width="160" src="assets/logo.png" alt="Logo" />
    </a>
</div>

<h3 align="center">
    Mido 2026
</h3>

<p align="center">
    El descargador **seguru** de Microsoft Windows
</p>

<p align="center">
    <a href="README.md">English</a> · <a href="README.es.md">Español</a> · <b>Asturianu</b>
</p>

Mido ye un veceru de descarga seguru y de códigu abiertu pa l'API propietaria de descarga de Microsoft (inxeniería inversa). ¡Les descargues vienen de los servidores **oficiales** de Microsoft y namás tienes qu'executar un comandu pa dir del principiu al final en un intre!

Inclúi carauterístiques avanzaes como la continuación de descargues, la verificación de la suma SHA-256, la lóxica de reintentos, ISOs multillingües (inglés, español y español de Méxicu) y la descarga de munches versiones de Windows nun solu comandu. ¿Díxique ta escritu en *puru* POSIX sh (con unos pocos coreutils) más curl, asina que funciona en cualquier llau (¡inclusive en Windows con WSL o con un shell de Cygwin)? Tan robustu y, eso sí, bien minimalista!

> **Actualización de 2026:** Microsoft sustituyó l'antigua API de descarga HTML por una API JSON `software-download-connector` protexida por un serviciu anti-abusu (Microsoft Sentinel). Mido 2026 implementa la nueva API y la negociación necesaria, arregla dellos fallos del shell, añade lóxica de reintentos y añade soporte pa la llingua española. Mira [docs/TECHNICAL.md](docs/TECHNICAL.md) pa los detalles.

#### ❌ La ferramienta de creación de medios de Microsoft (`mediacreationtool.exe`, software infláu y propietariu)

<p align="center">
    <img src="assets/bloatware1.png" alt="Microsoft's proprietary bloatware executable"></img>
    <br />
    <img src="assets/bloatware2.png" width="250px" alt="Microsoft's proprietary bloatware"></img>
    <img src="assets/bloatware3.png" width="200px" alt="Microsoft's bloatware"></img>
</p>

Páxina web inflada: `https://www.microsoft.com/en-us/software-download/windows11`
- Mido ufierta exactamente les mesmes descargues que esta páxina (usa la mesma API)

#### ✅ Mido (usa los **mesmos** servidores oficiales de Microsoft; <img src="https://awesome.re/badge.svg" style="position: relative; top: 5px;"></img> software de códigu abiertu)

<p align="center">
    <img src="assets/demo.gif" width="400" alt="Project demo GIF"></img>
</p>

## Llograr Mido

Llogra [Mido.sh](https://raw.githubusercontent.com/BENDER35/Mido_2026/main/Mido.sh) abriendo l'enllaz, faciendo clic derechu y escoyendo «Guardar [páxina] como...»

### Requisitos

Mido ta escritu en **POSIX `sh` puru** y namás rique unes poques ferramientes que yá tán presentes en casi toos los sistemes Linux. Nuna instalación d'escritoriu normal **nun** tienes que instalar nada sacáu, en dalgunes imáxines minimalistes, **`curl`**.

| Ferramienta | Úsase pa | Paquete |
|---|---|---|
| `sh` | running the script (Dash is preferred and used automatically if present) | `dash` / `bash` |
| `curl` | talking to Microsoft's servers | `curl` |
| `sha256sum` | verifying downloaded ISOs | `coreutils` |
| `grep`, `sed`, `tr`, `cut`, `head`, `tail`, `fold`, `printf` | parsing the download API responses | `coreutils`, `grep`, `sed` |
| `tput` | colored output (optional) | `ncurses` |
| `uuidgen` | generating a session ID **only** on systems without `/proc` (macOS/BSD — never needed on Linux) | `util-linux` / built-in on macOS |

Si falta daqué, instálalu col xestor de paquetes de la to distribución:

| Distribución | Comandu |
|---|---|
| Debian / Ubuntu / Mint / Pop!\_OS / Kali / Raspberry Pi OS | `sudo apt update && sudo apt install -y curl` |
| Fedora | `sudo dnf install -y curl` |
| RHEL / CentOS Stream / Rocky / AlmaLinux | `sudo dnf install -y curl` (older: `sudo yum install -y curl`) |
| openSUSE Leap / Tumbleweed / SLES | `sudo zypper install -y curl` |
| Arch / Manjaro / EndeavourOS / Garuda | `sudo pacman -S --needed curl` |
| Alpine | `sudo apk add curl` |
| Void | `sudo xbps-install -S curl` |
| Gentoo / Funtoo | `sudo emerge net-misc/curl` |
| NixOS | `nix-shell -p curl` (or add `pkgs.curl` to `environment.systemPackages`) |
| Slackware | `sudo slackpkg install curl` |
| Solus | `sudo eopkg install curl` |
| Clear Linux | `sudo swupd bundle-add curl` |
| FreeBSD | `sudo pkg install curl` (coreutils and util-linux are preinstalled) |
| macOS | `curl` and `uuidgen` are preinstalled; install GNU coreutils for `sha256sum`: `brew install coreutils` and add `$(brew --prefix coreutils)/libexec/gnubin` to your `PATH` |

> Pues confirmar que too ta nel so sitiu executando `./Mido.sh --help`, que amuesa la llista de medios ensin tocar la rede.

### Mac y Linux

Descarga `Mido.sh`, abre un terminal, da-y permisos d'execución al ficheru y executalu (como se ve nel GIF de riba):

```sh
chmod +x Mido.sh
./Mido.sh win11x64
```

### Windows

Mido ye un script de shell POSIX, asina que **nun** funciona de forma nativa en `cmd.exe` o PowerShell — en Windows executalo dientro de **WSL (Subsistema de Windows pa Linux)**, que ufierta un entornu Linux real. Esta ye la forma encamentada y totalmente soportada. Les envoltories `Mido.bat` / `Mido.ps1` incluyíes detecten WSL y faenlo por ti (mira *Envoltories natives de Windows* más abaxo).

Tamién puedes usar una capa d'emulación POSIX como [Cygwin](https://www.cygwin.com/install.html) o [MSYS2](https://www.msys2.org/#installation), instalaes con un solu comandu con WinGet:

Alternatively you can use a POSIX emulation layer such as
[Cygwin](https://www.cygwin.com/install.html) or
[MSYS2](https://www.msys2.org/#installation), installed in one command with
WinGet:

```
winget install -e --id Cygwin.Cygwin
winget install -e --id MSYS2.MSYS2
```

#### Pasu 1 — Instalar WSL en Windows 11

Abre **PowerShell como Alministrador** (clic derechu nel botón d'Entamu → *Terminal (Alministrador)*) y executa:

```powershell
wsl --install
```

Esto habilita les carauterístiques de Windows necesaries, instala'l nucleu Linux de WSL2 y una distribución Linux (Ubuntu por defecto) y pídeque reinicies. Dempués del reiniciu, Ubuntu abre y pídeque crees un nome d'usuariu y una contraseña. Yá ta.

#### Pasu 1 (alternativu) — Instalar WSL en Windows 10

> **Estáu del soporte de Windows 10.** Windows 10 llegó al **fin del soporte'l 14 d'ochobre de 2025**. Los dispositivos Home/Pro puen xunise al programa **Consumer ESU**, que namás apurre actualizaciones de seguridá hasta'l **13 d'ochobre de 2026**. Los dispositivos comerciales (Enterprise/Education/Pro usaos comercialmente) llogren hasta **tres años** d'ESU — l'Añu 1 acaba'l **13 d'ochobre de 2026**, l'Añu 2 el **12 d'ochobre de 2027** y l'Añu 3 el **10 d'ochobre de 2028**. WSL2 sigue funcionando en Windows 10, pero'l sistema operativu namás recibe arreglos mientres siga en serviciu. Mira la [FAQ d'ESU](https://learn.microsoft.com/en-us/lifecycle/faq/extended-security-updates).

**Windows 10 versión 2004 (compilación 19041) y posteriores** — que ye toa la instalación 22H2 anguaño soportada — usen el mesmu comandu únicu que Windows 11:

```powershell
wsl --install
```

En **compilaciones antigües de Windows 10**, y en **Windows 10 LTSC / IoT Enterprise LTSC** (que nun traen la Microsoft Store), `wsl --install` pue faltar o nun poder baxar una distribución. Usa meyor los pasos manuales:

1. Busca **«Activar o desactivar carauterístiques de Windows»**, habilita **Subsistema de Windows pa Linux** y **Plataforma de máquina virtual** y reinicia. (Comandos equivalents: `dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart` y lo mesmo pa `VirtualMachinePlatform`.)
2. Pa usar WSL2, instala'l **paquete d'actualización del nucleu Linux de WSL2** dende <https://aka.ms/wsl2kernel> y reinicia (`wsl --update` tamién funciona una vegada WSL ta instaláu).
3. Instala una distribución. Ensin la Store, descarga'l ficheru de la distro dende <https://aka.ms/wslstore>, camuda'l nome del ficheru `.AppxBundle`/`.Appx` a `.zip`, descomprímilu y executa'l `ubuntu.exe` (o `debian.exe`, …) incluíu. Depués executa `wsl --set-default-version 2`.

#### Pasu 2 — Executar Mido

Dientro del shell de WSL, descarga Mido y executalu exactamente igual que en Linux:

```sh
curl -O https://raw.githubusercontent.com/BENDER35/Mido_2026/main/Mido.sh
chmod +x Mido.sh
./Mido.sh win11x64
```

Los ficherus guarden nel directoriu actual de WSL; usa `/mnt/c/Users/<you>/Downloads` si los quies na unidá de Windows.

#### Windows Server

WSL ta soportáu en **Windows Server 2019 y posteriores**. Les versiones de Server correspuenden a les versiones d'escritoriu asina:

| Escritoriu | Equivalente en Server | Soporte WSL |
|---|---|---|
| Windows 10 1809 | Windows Server 2019 | **WSL 1 only** (build 17763 < 18362); no `wsl --install`, enable the feature manually |
| Windows 11 21H2 / 22H2 | Windows Server 2022 | WSL 1 & 2 — `wsl --install` |
| Windows 11 24H2 | Windows Server 2025 | WSL 1 & 2, including Server Core — `wsl --install` |

- **Windows Server 2022 / 2025:** nun PowerShell d'**alministrador**, executa `wsl --install` y reinicia. La Microsoft Store nun ta disponible en Server, asina que `wsl --install` (o `wsl --install -d <Distro>`) baxa la distribución direutamente.
- **Windows Server 2019 (y Server Core):** executa `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux, VirtualMachinePlatform` nun PowerShell d'alministrador, reinicia ya instala una distribución a mano (los paquetes de `aka.ms/wslstore`). WSL2 rique la compilación 18362+, asina que Server 2019 ta llindáu a **WSL 1**.

#### Envoltories natives de Windows

Mido inclúi envoltories natives de Windows por comodidá:

- **`Mido.bat`** - envoltoria de ficheru batch (execútase con doble clic o dende cmd)
- **`Mido.ps1`** - envoltoria de PowerShell (execútase dende PowerShell)

Dambes envoltories detecten WSL automáticamente y executen `Mido.sh` per mediu d'él, pasando toos los argumentos. Exemplu d'usu:

```
Mido.bat win11x64
Mido.ps1 win10x64 win11x64
```

Si WSL nun ta instaláu, les envoltories piden que lo instales.

## Úsu

```
./Mido.sh <windows_media>...
```

Exemplos:

```
./Mido.sh win11x64                       # Windows 11, English (US)
MIDO_LANG=es-ES ./Mido.sh win10x64       # Windows 10, Spanish (Spain)
MIDO_LANG=es-MX ./Mido.sh win11x64       # Windows 11, Mexican Spanish
./Mido.sh win7x64-ultimate win10x64      # Multiple ISOs in one command
./Mido.sh all                            # Every supported media
```

### Medios disponibles

| Argumentu | Descripción |
|---|---|
| `vista-x64-sp1` | Windows Vista SP1 x64 (English, sourced from archive.org) |
| `vista-x86-sp1` | Windows Vista SP1 x86 (English, sourced from archive.org) |
| `vista-es-x64-sp1` | Windows Vista SP1 x64 Spanish (sourced from archive.org) |
| `vista-es-x86-sp1` | Windows Vista SP1 x86 Spanish (sourced from archive.org) |
| `vista-x64-sp2` | Windows Vista **Enterprise** SP2 x64 (English, sourced from archive.org) |
| `vista-x86-sp2` | Windows Vista **Enterprise** SP2 x86 (English, sourced from archive.org) |
| `vista-es-x64-sp2` | Windows Vista **Enterprise** SP2 x64 Spanish (sourced from archive.org) |
| `vista-es-x86-sp2` | Windows Vista **Enterprise** SP2 x86 Spanish (sourced from archive.org) |
| `vista-x64-sp2-n` | Windows Vista N SP2 x64 (Home Basic N + Business N, English, sourced from archive.org) |
| `vista-x64-sp2-business-n` | Windows Vista Business N SP2 x64 (English, sourced from archive.org) |
| `vista-x86-sp2-business-n` | Windows Vista Business N SP2 x86 (English, sourced from archive.org) |
| `win7x64-ultimate` | Windows 7 Ultimate x64 (sourced from the Wayback Machine) |
| `win7x64-ultimate-esp` | Windows 7 Ultimate x64 Spanish — Microsoft official first, archive.org fallback |
| `win7x86-ultimate` | Windows 7 Ultimate x86 (32-bit, sourced from archive.org) |
| `win7x86-ultimate-esp` | Windows 7 Ultimate x86 Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win81x64` | Windows 8.1 x64 (**retired by Microsoft**, use the Enterprise Eval) |
| `win81x64-ent-32-espa` | Windows 8.1 Enterprise 32-bit Spanish (sourced from archive.org) |
| `win81x64-ent-64-esp` | Windows 8.1 Enterprise 64-bit Spanish (sourced from archive.org) |
| `win10x64` | Windows 10 x64 (multi-edition) |
| `win10x64-esp` | Windows 10 x64 Spanish (Spain) — Microsoft official first, archive.org fallback |
| `win10x64-es-mx` | Windows 10 x64 Spanish (Mexico) — Microsoft official first, archive.org fallback |
| `win10x86` | Windows 10 x86 (32-bit, multi-edition) — Microsoft official first, archive.org fallback |
| `win10x86-esp` | Windows 10 x86 (32-bit) Spanish (Spain) — Microsoft official first, archive.org fallback |
| `win10x86-es-mx` | Windows 10 x86 (32-bit) Mexican Spanish — Microsoft official first, archive.org fallback |
| `win11x64` | Windows 11 x64 (multi-edition) |
| `win11x64-esp` | Windows 11 x64 Spanish (Spain) — Microsoft official first, archive.org fallback |
| `win11x64-es-mx` | Windows 11 x64 Spanish (Mexico) — Microsoft official first, archive.org fallback |
| `win81x64-enterprise-eval` | Windows 8.1 Enterprise Evaluation |
| `win81x64` | Windows 8.1 x64 (**retired by Microsoft**, use the Enterprise Eval) |
| `win81x86` | Windows 8.1 x86 (32-bit, archive.org) |
| `win81x64-ent-32-espa` | Windows 8.1 Enterprise 32-bit Spanish (sourced from archive.org) |
| `win81x64-ent-64-esp` | Windows 8.1 Enterprise 64-bit Spanish (sourced from archive.org) |
| `win10x64-enterprise-eval` | Windows 10 Enterprise Evaluation |
| `win10x86-enterprise-eval` | Windows 10 Enterprise 32-bit Evaluation (LTSC 21H2 x86, sourced from archive.org) |
| `win11x64-enterprise-eval` | Windows 11 Enterprise Evaluation |
| `win10x64-enterprise-ltsc-eval` | Windows 10 Enterprise LTSC Evaluation (most secure) |
| `win11x64-enterprise-ltsc-eval` | Windows 11 Enterprise LTSC Evaluation (most secure) |
| `win11x64-iot-enterprise-ltsc-eval` | Windows 11 IoT Enterprise LTSC Evaluation |
| `win11x64-iot-enterprise-26h2` | Windows 11 IoT Enterprise 26H2 (annual channel, English, sourced from archive.org) |
| `win11x64-iot-enterprise-ltsc-2024` | Windows 11 IoT Enterprise LTSC 2024 (full, English, x64, sourced from archive.org) |
| `win10x64-iot-enterprise-22h2` | Windows 10 IoT Enterprise 22H2 (annual channel, English, x64, sourced from archive.org) |
| `win10x64-iot-enterprise-ltsc-2021` | Windows 10 IoT Enterprise LTSC 2021 (full, English, x64, sourced from archive.org) |
| `win10x64-iot-enterprise-ltsc-2019` | Windows 10 IoT Enterprise LTSC 2019 (full, English, x64, sourced from archive.org) |
| `win10x86-iot-enterprise-ltsc-2019` | Windows 10 IoT Enterprise LTSC 2019 (full, English, x86/32-bit, sourced from archive.org) |
| `win81-serial` | Windows 8.1 setup serial keys (for installation, not activation) |
| `win11x64-enterprise-ltsc-2024` | Windows 11 Enterprise LTSC 2024 (full, English, x64, sourced from archive.org) |
| `win11x64-enterprise-ltsc-2024-esp` | Windows 11 Enterprise LTSC 2024 (full, Spanish (Spain), x64, sourced from archive.org) |
| `win11x64-enterprise-ltsc-2024-es-mx` | Windows 11 Enterprise LTSC 2024 (full, Mexican Spanish, x64, sourced from archive.org) |
| `win10x64-enterprise-ltsc-2021` | Windows 10 Enterprise LTSC 2021 (full, English, x64, sourced from archive.org) |
| `win10x64-enterprise-ltsc-2021-esp` | Windows 10 Enterprise LTSC 2021 (full, Spanish (Spain), x64, sourced from archive.org) |
| `win10x64-enterprise-ltsc-2021-es-mx` | Windows 10 Enterprise LTSC 2021 (full, Mexican Spanish, x64, sourced from archive.org) |
| `win10x86-enterprise-ltsc-2021` | Windows 10 Enterprise LTSC 2021 (full, English, x86/32-bit, sourced from archive.org) |
| `win10x86-enterprise-ltsc-2021-esp` | Windows 10 Enterprise LTSC 2021 (full, Spanish (Spain), x86/32-bit, sourced from archive.org) |
| `win10x86-enterprise-ltsc-2021-es-mx` | Windows 10 Enterprise LTSC 2021 (full, Mexican Spanish, x86/32-bit, sourced from archive.org) |
| `win10x64-enterprise-ltsb-2016` | Windows 10 Enterprise LTSB 2016 (full, English, x64, sourced from archive.org) |
| `win10x64-enterprise-ltsb-2016-esp` | Windows 10 Enterprise LTSB 2016 (full, Spanish, x64, sourced from archive.org) |
| `win10x86-enterprise-ltsb-2016` | Windows 10 Enterprise LTSB 2016 (full, English, x86/32-bit, sourced from archive.org) |
| `win10x86-enterprise-ltsb-2016-esp` | Windows 10 Enterprise LTSB 2016 (full, Spanish, x86/32-bit, sourced from archive.org) |
| `win10x64-enterprise-ltsb-2015` | Windows 10 Enterprise LTSB 2015 (full, English, x64, sourced from archive.org) |
| `win10x64-enterprise-ltsb-2015-esp` | Windows 10 Enterprise LTSB 2015 (full, Spanish, x64, sourced from archive.org) |
| `win10x86-enterprise-ltsb-2015` | Windows 10 Enterprise LTSB 2015 (full, English, x86/32-bit, sourced from archive.org) |
| `win10x86-enterprise-ltsb-2015-esp` | Windows 10 Enterprise LTSB 2015 (full, Spanish, x86/32-bit, sourced from archive.org) |
| `win2008r2` | Windows Server 2008 R2 SP1 (x64, English) |
| `win2008r2-espa` | Windows Server 2008 R2 SP1 (x64, Spanish eval, sourced from archive.org) |
| `win2008-server-x64` | Windows Server 2008 SP2 (x64, English, all editions, sourced from archive.org) |
| `win2008-server-x64-espa` | Windows Server 2008 SP2 (x64, Spanish, all editions, sourced from archive.org) |
| `win2008-server-x86` | Windows Server 2008 SP2 (x86, English, all editions, sourced from archive.org) |
| `win2008-server-x86-espa` | Windows Server 2008 SP2 (x86, Spanish, all editions, sourced from archive.org) |
| `win2000-pro` | Windows 2000 Professional SP4 (English, sourced from archive.org) |
| `win2000-pro-espa` | Windows 2000 Professional SP4 Spanish (sourced from archive.org) |
| `win2000-pro-oem` | Windows 2000 Professional SP3 OEM (English, sourced from archive.org) |
| `win2000-pro-retail` | Windows 2000 Professional SP4 Retail (English, sourced from archive.org) |
| `win2000-pro-oem-espa` | Windows 2000 Professional SP1 OEM Spanish (sourced from archive.org) |
| `win2000-pro-retail-espa` | Windows 2000 Professional RTM Retail Spanish (sourced from archive.org) |
| `win-xp-pro` | Windows XP Professional SP3 (English, sourced from archive.org) |
| `win-xp-pro-espa` | Windows XP Professional SP3 Spanish (sourced from archive.org) |
| `win-xp-pro-32` | Windows XP Professional x86 (32-bit, sourced from archive.org) |
| `win-xp-pro-32-espa` | Windows XP Professional x86 SP3 Spanish (32-bit, sourced from archive.org) |
| `win-xp-pro-64` | Windows XP Professional x64 SP2 (64-bit, English, sourced from archive.org) |
| `win-xp-home` | Windows XP Home Edition SP3 (English, sourced from archive.org) |
| `win-xp-home-espa` | Windows XP Home Edition SP3 Spanish (sourced from archive.org) |
| `win-xp-home-oem` | Windows XP Home Edition RTM OEM (English, sourced from archive.org) |
| `win-xp-home-retail` | Windows XP Home Edition SP3 Retail (English, sourced from archive.org) |
| `win-xp-home-oem-espa` | Windows XP Home Edition SP2 OEM Spanish (sourced from archive.org) |
| `win-xp-home-retail-espa` | Windows XP Home Edition SP3 Retail Spanish (sourced from archive.org) |
| `win-xp-pro-oem` | Windows XP Professional SP2 OEM (English, sourced from archive.org) |
| `win-xp-pro-retail` | Windows XP Professional SP3 Retail (English, sourced from archive.org) |
| `win-xp-pro-oem-espa` | Windows XP Professional SP2 OEM Spanish (sourced from archive.org) |
| `win-xp-pro-retail-espa` | Windows XP Professional SP1 Retail Spanish (sourced from archive.org) |
| `win7x64-pro` | Windows 7 Professional x64 (sourced from archive.org) |
| `win7x64-pro-esp` | Windows 7 Professional x64 Spanish — Microsoft official first, archive.org fallback |
| `win7x86-pro` | Windows 7 Professional x86 (32-bit, sourced from archive.org) |
| `win7x86-pro-esp` | Windows 7 Professional x86 Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win7x64-homepremium` | Windows 7 Home Premium x64 (sourced from archive.org) |
| `win7x64-homepremium-esp` | Windows 7 Home Premium x64 Spanish — Microsoft official first, archive.org fallback |
| `win7x86-homepremium` | Windows 7 Home Premium x86 (32-bit, sourced from archive.org) |
| `win7x86-homepremium-esp` | Windows 7 Home Premium x86 Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win7x64-enterprise` | Windows 7 Enterprise x64 (sourced from archive.org) |
| `win7x64-enterprise-esp` | Windows 7 Enterprise x64 Spanish — Microsoft official first, archive.org fallback |
| `win7x86-enterprise` | Windows 7 Enterprise x86 (32-bit, sourced from archive.org) |
| `win7x86-enterprise-esp` | Windows 7 Enterprise x86 Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win7x64-sp1` | Windows 7 SP1 x64 (sourced from archive.org) |
| `win7x86-sp1` | Windows 7 SP1 x86 (32-bit, sourced from archive.org) |
| `win81x64-pro` | Windows 8.1 Pro x64 — Microsoft retired automation, use archive.org |
| `win81x64-pro-esp` | Windows 8.1 Pro Spanish (Spain) — Microsoft official first, archive.org fallback |
| `win81x86-pro` | Windows 8.1 Pro x86 (32-bit, sourced from archive.org) |
| `win81x86-pro-esp` | Windows 8.1 Pro x86 Spanish (32-bit) — Microsoft official first, archive.org fallback |
| `win2003-server` | Windows Server 2003 Enterprise (x86, English, sourced from archive.org) |
| `win2003-server-espa` | Windows Server 2003 Enterprise (x86, Spanish, sourced from archive.org) |
| `win2003-server-x64` | Windows Server 2003 R2 Enterprise x64 SP2 (English, sourced from archive.org) |
| `win2003-server-x64-espa` | Windows Server 2003 R2 Enterprise x64 SP2 (Spanish, sourced from archive.org) |
| `win2012r2-eval` | Windows Server 2012 R2 Evaluation (sourced from archive.org, updated ISO) |
| `win2016-essentials` | Windows Server 2016 Essentials x64 English (sourced from archive.org) |
| `win2019-iso` | Windows Server 2019 x64 English (full ISO, sourced from archive.org) |
| `win2000-server` | Windows 2000 Server SP4 (English, sourced from archive.org) |
| `win2000-server-espa` | Windows 2000 Server SP4 (Spanish, sourced from archive.org) |
| `win2000-advanced-server` | Windows 2000 Advanced Server SP1 (English, sourced from archive.org) |
| `win2000-advanced-server-espa` | Windows 2000 Advanced Server SP1 (Spanish, sourced from archive.org) |
| `win2000-datacenter` | Windows 2000 Datacenter Server RTM OEM (SP1, English, sourced from archive.org) |
| `win2000-datacenter-sp4` | Windows 2000 Datacenter Server SP4 (English, sourced from archive.org) |
| `win2012r2-eval` | Windows Server 2012 R2 Evaluation |
| `win2016-eval` | Windows Server 2016 Evaluation |
| `win2019-eval` | Windows Server 2019 Evaluation |
| `win2022-eval` | Windows Server 2022 Evaluation |
| `win2025-eval` | Windows Server 2025 Evaluation |
| `win2025-enterprise-64` | Windows Server 2025 Enterprise x64 (64-bit) |
| `win2025-enterprise-64-esp` | Windows Server 2025 Enterprise x64 Spanish (64-bit) |
| `win2025-datacenter-64` | Windows Server 2025 Datacenter x64 (64-bit) |
| `win2025-datacenter-64-esp` | Windows Server 2025 Datacenter x64 Spanish (64-bit) |
| `win2022-eval` | Windows Server 2022 Evaluation |
| `win2022-enterprise-64` | Windows Server 2022 Enterprise x64 (64-bit) |
| `win2022-enterprise-64-esp` | Windows Server 2022 Enterprise x64 Spanish (64-bit) |
| `win2022-datacenter-64` | Windows Server 2022 Datacenter x64 (64-bit) |
| `win2022-datacenter-64-esp` | Windows Server 2022 Datacenter x64 Spanish (64-bit) |
| `win2012r2-essentials-eval` | Windows Server 2012 R2 Essentials Evaluation |
| `win2016-essentials-eval` | Windows Server 2016 Essentials Evaluation |
| `win2019-essentials-eval` | Windows Server 2019 Essentials Evaluation |
| `hyperv2012-eval` | Hyper-V Server 2012 Evaluation |
| `hyperv2012r2-eval` | Hyper-V Server 2012 R2 Evaluation |
| `hyperv2016-eval` | Hyper-V Server 2016 Evaluation |
| `hyperv2019-eval` | Hyper-V Server 2019 Evaluation |
| `win30` | Windows 3.0 (English, sourced from archive.org; ZIP of the original diskettes) |
| `win30-espa` | Windows 3.0a Spanish (sourced from archive.org; ZIP) |
| `win31` | Windows 3.1 (English, sourced from archive.org) |
| `win31-espa` | Windows 3.1 Spanish (sourced from archive.org) |
| `wfw31` | Windows for Workgroups 3.1 (English, build 014e, sourced from archive.org; RAR of the original diskettes) |
| `wfw31-espa` | Windows for Workgroups 3.1 Spanish (sourced from archive.org; 7z diskette images) |
| `win311` | Windows 3.11 / Windows for Workgroups 3.11 (English, sourced from archive.org) |
| `win311-espa` | Windows 3.11 Spanish (sourced from archive.org) |
| `wfw311` | Windows for Workgroups 3.11 (English, same media as `win311`, sourced from archive.org) |
| `wfw311-espa` | Windows for Workgroups 3.11 Spanish (sourced from archive.org) |
| `win95` | Windows 95 (English, sourced from archive.org) |
| `win95-espa` | Windows 95 Spanish (sourced from archive.org) |
| `win98` | Windows 98 Second Edition (English, sourced from archive.org) |
| `win98-espa` | Windows 98 Second Edition Spanish (sourced from archive.org) |
| `winme` | Windows Millennium Edition (English, sourced from archive.org) |
| `winme-espa` | Windows Millennium Edition Spanish (sourced from archive.org) |
| `winnt31` | Windows NT 3.1 Workstation (English, sourced from archive.org) |
| `winnt35` | Windows NT 3.5 Workstation (English, sourced from archive.org) |
| `winnt351` | Windows NT 3.51 Workstation (English, sourced from archive.org) |
| `winnt40` | Windows NT 4.0 Workstation (English, sourced from archive.org) |
| `backoffice` | Microsoft BackOffice Small Business Server 4.0 (x86, sourced from archive.org) |
| `winframe` | Citrix WinFrame 1.6 (Windows NT 3.51 Terminal Server Edition, sourced from archive.org) |

## Soporte de llingües

Define la variable d'ente `MIDO_LANG` con unu d'estos valores:

| Valor | Llingua |
|---|---|
| `en-US` | English (United States) — default |
| `es-ES` | Spanish (Spain) |
| `es-MX` | Spanish (Mexico) |

Les ISOs n'español tán disponibles pa les versiones **de consumidor** (`win10x64`, `win11x64`). Amás de `MIDO_LANG`, hai argumentos específicos pa español (España) y español de Méxicu: `win10x64-esp`, `win10x64-es-mx`, `win11x64-esp` y `win11x64-es-mx`. Estos prueben primero la descarga oficial de Microsoft y, si la petición se refuga (por exemplu pol sistema anti-abusu Sentinel), recurren a una copia idéntica d'archive.org. Amás, les ISOs de Windows 10 de 32 bits (x86) tán disponibles per mediu d'archive.org n'inglés (`win10x86`), d'España (`win10x86-esp`) y de Méxicu (`win10x86-es-mx`). Tamién hai ISOs de Windows 8.1 Enterprise n'español per mediu d'archive.org, tanto de 32 bits (`win81x64-ent-32-espa`) como de 64 bits (`win81x64-ent-64-esp`). Una ISO d'evaluación de Windows Server 2008 R2 SP1 n'español ta disponible per mediu d'archive.org (`win2008r2-espa`). Windows Server 2008 (non R2) SP2 ta disponible per mediu d'archive.org n'inglés y n'español, tanto de 64 bits (`win2008-server-x64`, `win2008-server-x64-espa`) como de 32 bits (`win2008-server-x86`, `win2008-server-x86-espa`). Windows Server 2003 Enterprise y Windows 2000 Server SP4 tamién tán disponibles n'inglés y n'español per mediu d'archive.org (`win2003-server`, `win2003-server-espa`, `win2000-server`, `win2000-server-espa`). La versión de 64 bits de Server 2003 R2 Enterprise x64 SP2 ta disponible n'inglés (`win2003-server-x64`) y n'español (`win2003-server-x64-espa`). Tamién hai ISOs de Windows Vista SP1 n'español per mediu d'archive.org (`vista-es-x64-sp1`, `vista-es-x86-sp1`). Les ISOs de Windows Vista **Enterprise** SP2 n'español tamién tán per mediu d'archive.org (`vista-es-x64-sp2`, `vista-es-x86-sp2`).

Los medios Windows Vista **N** (que satisfacen la resolución de la Xunión Europea y escluyen el software multimedia incluíu) tán disponibles n'inglés: el DVD x64 N all-in-one con Home Basic N y Business N (`vista-x64-sp2-n`) y les ISOs de Business N x64 y x86 (`vista-x64-sp2-business-n`, `vista-x86-sp2-business-n`). Ten en cuenta que **Windows Vista Enterprise N** nunca se publicó como mediu MSDN independiente (el DVD «N» namás trai Home Basic N y Business N) y que nun hai ISOs de Vista N n'español n'archive.org, asina que nun s'ufierten argumentos `-es`/`-es-mx` pa elles. Les ISOs de Windows 7 SP1 tamién tán n'inglés (`win7x64-sp1`, `win7x86-sp1`) per mediu d'archive.org. Los medios de Windows 7 Ultimate n'español ufiértense primero dende Microsoft y, si non, d'archive.org (`win7x64-ultimate-esp`), y les ISOs auténtiques d'SP1 n'español tán archivaes pa cada SKU (`win7x64-ultimate-esp`, `win7x86-ultimate-esp`, `win7x64-pro-esp`, `win7x86-pro-esp`, `win7x64-homepremium-esp`, `win7x86-homepremium-esp`, `win7x64-enterprise-esp`, `win7x86-enterprise-esp`). Windows 2000 Professional, Windows XP Professional, Windows XP Home Edition y Windows 7 Professional tamién tienen ISOs n'español per mediu d'archive.org. Les ISOs de Windows 8.1 Pro n'español tán tamién n'archive.org pa España (`win81x64-pro-esp`). Los medios Enterprise, Server y Evaluation, polo demás, son namás n'inglés, tal como los publica Microsoft. Les ISOs non ingleses guarden con un sufizu de llocalización (ex. `win11x64.es-MX.iso`) o con un nome de ficheru `-espa`/`-esp`/`-es-mexico`, según corresponda, asina que nunca sobrescriben les ingleses. Como Microsoft nun publica sumes de comprobación públiques pa cada llanzamientu llocalizáu, les ISOs llocalizaes puen amosar `NO KNOWN CHECKSUM (skipping verification)`.

Windows 2000 Professional y Windows XP tamién tán disponibles nos canales de llicencia orixinales **OEM** y **Retail** (FPP), n'inglés y n'español: `win2000-pro-oem`, `win2000-pro-retail`, `win2000-pro-oem-espa`, `win2000-pro-retail-espa`, `win-xp-home-oem`, `win-xp-home-retail`, `win-xp-home-oem-espa`, `win-xp-home-retail-espa`, `win-xp-pro-oem`, `win-xp-pro-retail`, `win-xp-pro-oem-espa` y `win-xp-pro-retail-espa`. Windows XP Home Edition y Windows XP Professional tán disponibles n'inglés y n'español (`win-xp-home`, `win-xp-home-espa`, `win-xp-pro`, `win-xp-pro-espa`, `win-xp-pro-32-espa`).

Windows 2000 Professional and Windows XP are additionally available in the
original **OEM** and **Retail** (FPP) license channels, in English and Spanish:
`win2000-pro-oem`, `win2000-pro-retail`, `win2000-pro-oem-espa`,
`win2000-pro-retail-espa`, `win-xp-home-oem`, `win-xp-home-retail`,
`win-xp-home-oem-espa`, `win-xp-home-retail-espa`, `win-xp-pro-oem`,
`win-xp-pro-retail`, `win-xp-pro-oem-espa` and `win-xp-pro-retail-espa`.
Windows XP Home Edition and Windows XP Professional are available in English and
Spanish (`win-xp-home`, `win-xp-home-espa`, `win-xp-pro`, `win-xp-pro-espa`,
`win-xp-pro-32-espa`).

> **Nota sobre la disponibilidad del español de Méxicu:** Microsoft namás llocalizó Windows a una compilación separada n'español de Méxicu (`es-MX`) a partir de **Windows 10**. Les versiones anteriores (Windows 3.x, 95, 98, Me, NT, 2000, XP, Vista, Windows 7 y Windows 8/8.1) publicáronse con una **sola compilación n'español** (`es-ES`) que sirve pa toles rexones hispanofalantes, asina que nun s'ufierten argumentos `-es-mx` pa elles. Los argumentos `-es-mx` auténticos (y `MIDO_LANG=es-MX`) esisten por tanto namás pa Windows 10, Windows 11 y les sos ediciones Enterprise LTSC, pa les que Microsoft publicó medios separaos d'español (Méxicu). Los antiguos argumentos `-es-mx` de Windows 7 y Windows 8.1 retiróronse porque los espeyos d'archive.org d'eses compilaciones (nunca oficiales) dexaron de tar disponibles.

Los llanzamientos retro (`win30`, `win31`, `wfw31`, `win311`/`wfw311`, `win95`, `win98`, `winme`, `winnt31`, `winnt35`, `winnt351`, `winnt40`, `backoffice`, `winframe`) vienen de `archive.org` y nun tienen sumes de comprobación publicaes. Windows 3.x, Windows 95, Windows 98 Second Edition y Windows Millennium Edition tamién tán n'español (`win30-espa`, `win31-espa`, `wfw31-espa`, `win311-espa`, `wfw311-espa`, `win95-espa`, `win98-espa`, `winme-espa`); el restu de llanzamientos retro son namás n'inglés. Ten en cuenta que `win311` y `wfw311` se refieren al mesmu productu (Windows 3.11 for Workgroups) y comparten el mesmu mediu n'inglés; los llanzamientos n'español estremen un mediu de «Windows 3.11» (`win311-espa`) d'un mediu de «Windows 3.11 pa Trabayu en Grupu» (`wfw311-espa`). Como Microsoft nunca llocalizó estes versiones al español de Méxicu, nun esisten argumentos `-es-mx` pa elles.

## Cómo funciona Mido?

Interactúa cola API propietaria de descarga de Microsoft pa llograr la última versión de Windows y xenerar un enllaz de descarga nuevu (válidu 24 hores). Depués baxa'l ficheru, verifica la so suma SHA-256 y sigue automáticamente coles descargues parciales.

En detalle:

1. Rasca la páxina oficial de descarga pa llograr el *ID d'edición del productu* de la versión pidida.
2. Pon nuna llista blanca un *ID de sesión* aleatoriu y completa la negociación anti-abusu *Sentinel* de Microsoft.
3. Consulta l'API JSON pol *ID de SKU* que correspuende a la llingua pidida.
4. Pide un enllaz de descarga nuevu y baxa'l ficheru a `<file>.PART`.
5. Verifica la suma SHA-256 y camuda'l nome del ficheru a `<file>` (correutu) o a `<file>.UNVERIFIED` (rique revisión manual).

Mira [docs/TECHNICAL.md](docs/TECHNICAL.md) pa una esplicación completa.

## Qué más pue faer Mido?

Aparté de les versiones de consumidor de Windows como la 11 y la 10, tamién pue baxar automáticamente la última versión de Server (ex. Windows Server 2025) y les ediciones Enterprise de toles versiones de Windows dende Windows 7 (o Windows Server 2008 R2, ya inclusive Server 2003 y 2000 Server). Tamién s'inclúin llanzamientos retro dende principios de los años 90: Windows 3.0, Windows 3.1, Windows for Workgroups 3.1, Windows 3.11 for Workgroups, Windows 95, Windows 98 Second Edition, Windows Millennium Edition, Windows NT 3.1, 3.5 y 3.51 Workstation y Windows NT 4.0 Workstation (`win30`, `win31`, `wfw31`, `win311`/`wfw311`, `win95`, `win98`, `winme`, `winnt31`, `winnt35`, `winnt351`, `winnt40`, toos d'archive.org). Tamién ufierta los productos de servidor de la dómina de Windows NT 3.51 Microsoft BackOffice Small Business Server 4.0 (`backoffice`) y Citrix WinFrame 1.6 (Windows NT 3.51 Terminal Server Edition, `winframe`). Windows 3.x, Windows 95, 98 y Millennium Edition tamién tienen medios n'español (`win30-espa`, `win31-espa`, `wfw31-espa`, `win311-espa`, `wfw311-espa`, `win95-espa`, `win98-espa`, `winme-espa`) y Windows 2000 Professional / Windows XP vienen nos canales de llicencia orixinales OEM y Retail (mira la tabla de medios de riba).

Quies una instalación de Windows más segura y minimalista dende'l primer momentu que Microsoft ufierta oficialmente? Entós baxa la versión LTSC de Windows. Trae muncho menos «inflazu» y soporta'l mou de telemetría [«Security»](https://learn.microsoft.com/en-us/windows/privacy/configure-windows-diagnostic-data-in-your-organization#diagnostic-data-settings) de Microsoft (amás de tener soporte a llargu plazu).

### Ediciones IoT Enterprise

Windows **IoT Enterprise** ye la edición que Microsoft ufierta pa dispositivos dedicaos (caxeros, quioscos, cartelería dixital, equipos médicos, thin clients). Ye'l mesmu sistema operativu que Enterprise, pero con **requisitos de hardware relaxaos** — nun rique TPM, Secure Boot, UEFI nin 4 GB de RAM — y, nes versiones LTSC, hasta **10 años** d'actualizaciones de seguridá. Mido ufierta les ISOs completes (non d'evaluación) n'inglés:

| Argumentu | Productu | Arquiteutura | Fin del soporte |
|---|---|---|---|
| `win11x64-iot-enterprise-ltsc-2024` | Windows 11 IoT Enterprise LTSC 2024 | x64 | Oct 2034 |
| `win11x64-iot-enterprise-26h2` | Windows 11 IoT Enterprise 26H2 (annual channel) | x64 | per annual channel |
| `win10x64-iot-enterprise-22h2` | Windows 10 IoT Enterprise 22H2 (annual channel) | x64 | Windows 10 ESU |
| `win10x64-iot-enterprise-ltsc-2021` | Windows 10 IoT Enterprise LTSC 2021 | x64 | 2032-01-13 |
| `win10x64-iot-enterprise-ltsc-2019` | Windows 10 IoT Enterprise LTSC 2019 | x64 | 2029-01-09 |
| `win10x86-iot-enterprise-ltsc-2019` | Windows 10 IoT Enterprise LTSC 2019 | x86 (32-bit) | 2029-01-09 |

> **Sobre la disponibilidad de 32 bits:** Microsoft namás publicó una ISO x86 (32 bits) pa la versión **IoT Enterprise LTSC 2019**. El Windows 10 IoT Enterprise 22H2 del canal añal y el más reciente IoT Enterprise LTSC 2021 esisten **namás en x64** (y arm64), asina que nun hai ISO de 32 bits pa ellos.

### Claves de serie de configuración de Windows 8.1

Les siguientes claves de configuración puen usase mientres la instalación de Windows 8.1 pa pasar de la pantalla de configuración, pero **non activen Windows**:

- **Windows 8.1 Enterprise (x64):** `NF3MJ-Q9W3X-7Y9K3-RJR8C-3X3CQ`
- **Windows 8.1 Enterprise (x86 32-bit):** `KJYVW-2R2YX-2V9Y2-T8YV3-WRHYD`
- **Windows 8.1 Pro (x64):** `GCRJD-8NW9H-F2HCX-Y66R6-C8BMC`
- **Windows 8.1 Pro (x86 32-bit):** `W83YN-4R2Y3-2FWY4-T92JY-W3YXK`
- **Windows 8.1 N (x64):** `MWMSY-FVGGT-6X7JK-PPMRW-TRT0D`
- **Windows 8.1 N (x86 32-bit):** `VBN20-8R2YX-7WJ4C-YJJ26-G3YXK`

> **Importante:** Estes son claves de llicencia por volume / de configuración que permiten completar la instalación. **Non** apurren una copia de Windows con llicencia y activada. Pa activar, tienes d'usar una clave de productu xenuina o una llicencia dixital.

### Claves de serie de configuración de Windows Embedded

Les siguientes claves de configuración puen usase mientres la instalación de les ediciones de Windows Embedded pa pasar de la pantalla de configuración, pero **non activen Windows**:

**Windows Embedded 8.1 Industry**

- **Windows Embedded 8.1 Industry Pro:** `NMMPB-38DD4-R2823-62W8D-VXKJB`
- **Windows Embedded 8.1 Industry Enterprise:** `FNFKF-PWTVT-9RC8H-32HB2-JB34X`
- **Windows Embedded 8.1 Industry (180-day evaluation ISO):** `N3QJR-YCWKK-RVJGK-GQFMX-T82BF`

**Windows Embedded 8 Industry**

- **Windows Embedded 8 Industry Professional:** `RYXVT-BNQG7-VD29F-DBMRY-HT73M`
- **Windows Embedded 8 Industry Enterprise:** `NKB3R-R2F8T-3XCDP-7Q2KW-XWYQ2`

**Windows Embedded 8 Standard**

- **Windows Embedded 8 Standard runtime image (180-day evaluation key):** `MPVW4-JNF4W-MGM8P-G2WYY-CWDFY`
- **Windows Embedded 8 Standard toolkit — Image Configuration Editor (also licenses the toolkit in perpetuity):** `D9XDH-78JX9-2KJG8-K7CY9-KF2Q3`

> **Nota:** Microsoft nunca defines una clave de configuración de veceru KMS (GVLK) pa Windows Embedded 8 Standard, asina que s'ufierten les claves d'evaluación y de ferramientes de riba. Toes estes claves namés permiten siguir cola configuración; **non** activen Windows. Pa activar, usa una clave de productu xenuina.

### Ediciones Enterprise LTSC y LTSB

Mido tamién ufierta les ISOs completes (non d'evaluación) d'**Enterprise** con serviciu a llargu plazu, n'inglés y n'español (d'España y de Méxicu onde Microsoft les publicó). Son les mesmes ediciones LTSC/LTSB que les descargues d'«Evaluation» de riba, pero cola llicencia normal (que nun caduca):

| Argumentu | Productu | Arquiteutura | Fin del soporte |
|---|---|---|---|
| `win11x64-enterprise-ltsc-2024` | Windows 11 Enterprise LTSC 2024 | x64 | 2029-10-09 |
| `win11x64-enterprise-ltsc-2024-esp` / `-es-mx` | Windows 11 Enterprise LTSC 2024 (es-ES / es-MX) | x64 | 2029-10-09 |
| `win10x64-enterprise-ltsc-2021` | Windows 10 Enterprise LTSC 2021 | x64 | 2027-01-12 |
| `win10x64-enterprise-ltsc-2021-esp` / `-es-mx` | Windows 10 Enterprise LTSC 2021 (es-ES / es-MX) | x64 | 2027-01-12 |
| `win10x86-enterprise-ltsc-2021` | Windows 10 Enterprise LTSC 2021 | x86 (32-bit) | 2027-01-12 |
| `win10x86-enterprise-ltsc-2021-esp` / `-es-mx` | Windows 10 Enterprise LTSC 2021 (es-ES / es-MX) | x86 (32-bit) | 2027-01-12 |
| `win10x64-enterprise-ltsb-2016` | Windows 10 Enterprise LTSB 2016 (= "LTSC 2016") | x64 | 2026-10-13 |
| `win10x64-enterprise-ltsb-2016-esp` | Windows 10 Enterprise LTSB 2016 (Spanish) | x64 | 2026-10-13 |
| `win10x86-enterprise-ltsb-2016` | Windows 10 Enterprise LTSB 2016 (= "LTSC 2016") | x86 (32-bit) | 2026-10-13 |
| `win10x86-enterprise-ltsb-2016-esp` | Windows 10 Enterprise LTSB 2016 (Spanish) | x86 (32-bit) | 2026-10-13 |
| `win10x64-enterprise-ltsb-2015` | Windows 10 Enterprise LTSB 2015 | x64 | ended 2025-10-14 |
| `win10x64-enterprise-ltsb-2015-esp` | Windows 10 Enterprise LTSB 2015 (Spanish) | x64 | ended 2025-10-14 |
| `win10x86-enterprise-ltsb-2015` | Windows 10 Enterprise LTSB 2015 | x86 (32-bit) | ended 2025-10-14 |
| `win10x86-enterprise-ltsb-2015-esp` | Windows 10 Enterprise LTSB 2015 (Spanish) | x86 (32-bit) | ended 2025-10-14 |

> **«LTSC 2016» y «LTSB 2016» son el mesmu productu.** Microsoft usó'l nome **LTSB** (Long-Term Servicing *Branch*) pa los llanzamientos de 2015 y 2016 y camudólu a **LTSC** (Long-Term Servicing *Channel*) col llanzamientu de 2019, asina que la ISO de 2016 apaez baxo cualquier de los dos nomes — nunca hubo un «Windows 10 LTSC 2016» separáu.
>
> **Sobre la disponibilidad de 32 bits:** toos los llanzamientos de Windows 10 LTSC/LTSB de riba traen ISOs x86 (32 bits) y x64. Windows 11 (incluyíu Enterprise LTSC 2024) ye **namás x64/arm64** — nun hai compilación de 32 bits. El Windows 11 Enterprise LTSC 2024 n'español esiste como es-ES y es-MX, pero les ISOs de Windows 10 LTSB 2015/2016 namás tuvieron una compilación n'español (es-ES), asina que nun hai nengún argumentu `-es-mx` pa elles.
>
> **IoT vs. Enterprise:** los argumentos `-iot-` son les ediciones IoT Enterprise (requisitos de hardware relaxaos); los de riba son les ediciones Enterprise normales. Pa la LTSC 2021 la variante IoT ta soportada hasta'l 2032-01-13, mientres que la variante Enterprise normal acaba'l 2027-01-12.

### Versión máxima d'Office instalable

Microsoft Office tien unos requisitos de sistema operativu propios y muncho más estrictos que los de Windows. La tabla d'abaxo amuesa **la versión más reciente d'Office que soporta oficialmente cada versión de Windows** — instalar daqué más modernu o bien lo bloquea l'instalador d'Office o bien queda ensin soporte:

| Versión de Windows | Office más reciente soportáu | Notes |
|---|---|---|
| Windows 95 | **Office 2000** | Office 2000 requires Windows 95 or later. |
| Windows 98 / Me / NT 4.0 | **Office XP (2002)** | Office XP requires Windows 98/Me/NT 4.0 SP6a. Office 2003 needs 2000 SP3/XP. |
| Windows 2000 | **Office 2003** | Requires Windows 2000 SP3. Office 2007 needs XP SP2/Vista. |
| Windows XP | **Office 2010** | Last version to support XP SP3; Office 2013+ require Windows 7. |
| Windows Vista | **Office 2010** | Last version to support Vista SP2; Office 2013+ require Windows 7. |
| Windows 7 | **Office 2016** | Office 2019+ officially require Windows 10. Microsoft 365 Apps ran on 7 until Jan 2023. |
| Windows 8 | **Office 2013** | Office 2016 requires Windows 8.1+, so Windows 8 is skipped. |
| Windows 8.1 | **Office 2016** | Microsoft 365 Apps ran on 8.1 until Jan 2023. |
| Windows 10 LTSB 2015 | **Office 2016** | Office 2019/2021/LTSC 2024 do not support the 2015/2016 LTSB builds. |
| Windows 10 LTSB 2016 | **Office 2016** | Office 2019/2021/LTSC 2024 do not support the 2015/2016 LTSB builds. |
| Windows 10 LTSC 2019 | **Office LTSC 2024** | Also Office 2019, Office LTSC 2021 and Microsoft 365. |
| Windows 10 LTSC 2021 | **Office LTSC 2024** | Also Office 2019, Office LTSC 2021 and Microsoft 365. |
| Windows 10 (22H2, Home/Pro/Enterprise) | **Office 2024 / Microsoft 365** | Office **LTSC** 2024 is *not* supported on 22H2 — use retail **Office 2024** there. |
| Windows 11 (incl. LTSC 2024) | **Office LTSC 2024 / Office 2024 / Microsoft 365** | Any current Office release. |
| Windows Server 2016 | **Office 2016** | Server matching its client generation. |
| Windows Server 2019 | **Office 2019 / LTSC 2021** | Server 2019 is listed for Office 2019. |
| Windows Server 2022 / 2025 | **Office LTSC 2024** | Office LTSC 2024 supports Server 2022 and 2025. |

> **Arquiteutura:** el Office de 64 bits apaeció per primer vegada con **Office 2010**; Office 2003 y 2007 son namés de 32 bits. El Office de 32 bits sigue siendo'l valor encamentáu pa una compatibilidá máxima de complementos — instala Office de 64 bits namás si lo precisas (ficheros de Excel/PowerPoint mui grandes, >2 GB de memoria por aplicación, o 4K+). Office de 64 bits rique Windows de 64 bits.
>
> **Instalar un Office más modernu del indicáu** dacuando ye posible con axustes (o con un nucleu estendíu — mira más abaxo), pero nun ta soportáu y pue romper partes de la suite.
>
> **Les ediciones de Server** siguen la xeneración de veceru na que se basen: Server 2008/2008 R2 (dómina de Vista/7) → Office 2010/2013, Server 2012/2012 R2 (dómina de 8/8.1) → Office 2013/2016, Server 2016 → Office 2016, Server 2019 → Office 2019, Server 2022/2025 → Office LTSC 2024.

## Activación del productu: la activación per teléfonu ta retirada, sustituyida pola activación per Internet

Windows NT 3.1, 3.5 y 3.51 (y toos los medios d'esti proyeutu anteriores a Windows XP) **nun tienen activación de productu nenguna**. Microsoft introdució l'activación de productu con Windows XP y Office XP en 2001, y depués usáronla Windows Vista, 7, 8.1, 10, 11, la llinia de Windows Server y Microsoft Office.

Microsoft **suprimió l'antiguu sistema d'activación per teléfonu** y **sustituyólu por un sistema d'activación per Internet**: el **Product Activation Portal**. A partir del **3 d'avientu de 2025**, l'automatización tradicional d'activación de productu per teléfonu pasó del teléfono a Internet. Si llames al antiguu númberu de teléfonu d'activación, agora oyes una grabación que te diz que uses el portal en llinia. L'activación ensin conexón sigue soportada — namás camudó la forma d'obtener el *Confirmation ID* (un portal web en llugar del sistema telefónicu automáticu).

Pasos pa la nueva activación per Internet:

1. Nel equipu que rique activación, abre'l diálogu d'activación de Windows y escueyi **Activar per teléfonu** (o executa `SLUI 04`) pa ver el **Installation ID**. L'equipu pue siguir ensin conexón — apunta l'ID.
2. Dende **cualquier otru dispositivu con accesu a Internet**, abre'l **Product Activation Portal**: <https://visualsupport.microsoft.com> (enllaz curtiu: `aka.ms/aoh`).
3. Completa'l CAPTCHA, escueyi **Proceed to Sign In** ya inicia sesión con una cuenta soportada (cuenta personal de Microsoft, cuenta de trabayu/escola, Microsoft Entra ID o un inquilín d'Azure Government). La cuenta d'accesu namás ye pa entrar de forma segura nel portal y **nun** ta venceyada a la llicencia del productu.
4. Escueyi **Activate a Microsoft product** ya introduz el **Installation ID** del pasu 1.
5. El portal devuelve un **Confirmation ID**.
6. Escribe'l **Confirmation ID** de vuelta nel diálogu d'activación del equipu ensin conexón pa terminar l'activación.
Windows 10 Pro for Workstations y Windows 10/11 Pro Education han d'activase per Internet y nun soporten el procedimientu ensin conexón. Los veceros de volume puen usar la ferramienta Volume Activation Management Tool (VAMT) pa activación per proxy o l'activación per KMS / Active Directory.

## NewShell ("Shell Technology Preview") pa Windows NT 3.51

L'escritoriu retro de Windows NT 3.1/3.5/3.51 ye l'antiguu shell de Program Manager/File Manager. En 1995 Microsoft publicó una anovación del shell llamada **NewShell** (tamién conocida como **Shell Technology Preview**) que tresllada al revés el shell d'Explorer de Windows 95 — barra de xeres, menú d'Entamu y escritoriu — a **Windows NT 3.51**. Ye una beta que namás sirvía pa amosar cómo se vería'l próximu Windows NT 4.0, y ye un parche mui popular pa dotar al NT clásicu d'un escritoriu usable.

> **Importante:** NewShell producíose **namás pa Windows NT 3.51**. **Nun** funciona en Windows NT 3.1 nin nel Windows NT 3.5 orixinal (compilación 807). Una vista previa temprana del «Chicago shell» sí se distribuyó cola compilación 854 de Windows NT 3.5 filtrada, que s'archiva por separáu (mira más abaxo).

Descargues:

- **Windows NT 3.51 NewShell / Shell Technology Preview** (dambes versiones públiques: 26 de mayu de 1995 y la «Shell Technology Preview Update» del 8 d'agostu de 1995): <https://winworldpc.com/product/newshell/beta>
- **Windows NT 3.5 build 854 NewShell** (vista previa temprana, `WINSRV.DLL` completa): <https://archive.org/details/newshell.3.50.854>
  — descarga direuta: <https://archive.org/download/newshell.3.50.854/NT_854_newshell.zip>

Instalación de **Windows NT 3.51 Shell Technology Preview**:

1. Instala primero Windows NT 3.51 Workstation (o Server), y ye encamentáu aplicar el service pack más reciente de NT 3.51.
2. Descarga l'archivu de NewShell y descomprímilu (contién los ficherus actualizaos del shell d'Explorer).
3. Llee'l `readme.wri` que vien nel archivu — ellí tán los comandos de copia exactos pa esa versión concreta. En curtio, l'actualización fai una copia de seguridá del shell orixinal, copia los nuevos ficherus del shell d'Explorer (por exemplu `shell32.dll`, `explorer.exe`, `comctl32.dll`, `shdocvw.dll`) en `%SystemRoot%\System32`, anueva'l rexistru y sustituye Program Manager / File Manager pol nuevu shell.
4. Fai una instantánea o una copia de seguridá del sistema enantes d'aplicar el parche, yá que NewShell ye una beta ensin soporte.
5. Reinicia. Tienes de llograr la barra de xeres y el menú d'Entamu al estilu de Windows 95.

Instalación de **Windows NT 3.5 build 854 NewShell**:

1. Copia los directorios `IDW`, `MSTOOLS` y `UI` de la carpeta `NEWSHELL`/`GUI` de los medios al directoriu de Windows (de normal `\WINNT35`).
2. Abre Panel de control → **Sistema** ya añade `%SystemRoot%\idw;%SystemRoot%\mstools;%SystemRoot%\ui` a la variable d'ente `Path`.
3. Reinicia.

## Windows Update en Windows ensin soporte

Los medios más nuevos nun lo son todo: una vegada instaláu Windows, tienes que actualizalo tamién. Microsoft foi zarrando gradualmente los servicios de los que dependíen les versiones antigües de Windows, asina que una instalación nueva pue nun atopa actualizaciones n'absolutu.

- **Windows Vista y anterior (incluyíu Windows Server 2008 y anterior).** El serviciu clásicu **Windows Update / Microsoft Update** ta zarráu pa siempre. Estos sistemes yá nun puen llegar a los servidores d'actualización de Microsoft por sigo mesmos.
- **Windows 7 / Windows Server 2008 R2.** Microsoft zarró **Microsoft Update pa «otros productos de Microsoft»** (Office, etc.), ya inclusive les actualizaciones de Windows namás funcionen dempués d'instalar un conxuntu d'actualizaciones previes. El proyeutu comunitariu *Legacy Update* restaura dambes coses.
- **Windows 8.1 / Windows Server 2012 R2.** El Windows Update normal sigue funcionando, pero estes versiones están fora de soporte, asina que namás les ferramientes de la comunidá indicaes embaxo siguen ufiertando la llista completa d'actualizaciones.

### Proyeutos comunitarios de resureición

Como Microsoft retiró los servicios orixinales, la comunidá los reconstruyó:

- **Legacy Update** — <https://legacyupdate.net/> — la opción encamentada pa **Windows 2000, XP, Vista, 7, 8, 8.1, 10 y 11** y les versiones equivalents de Windows Server (x86, x64, Itanium y ARM64). Fai de proxy del protocolu modernu Windows Update v6 escontra los servidores oficiales de Microsoft ya instala automáticamente les actualizaciones previes que falten nes instalaciones nueves (soporte de firma de códigu SHA-2 `KB4474419`/`KB4490628`, l'actualización de la pila de serviciu, y más).
- **Windows Update Restored** — <https://windowsupdaterestored.com/> — el proyeutu hermanu que resucita les páxines **orixinales** de Windows Update y Microsoft Update pa **Windows 95, NT 4.0, 98, Me, 2000 y XP**. Rique **Internet Explorer 4.0–6.0** (IE 5.5 a 800×600 da l'aspeutu clásicu) y ta fechu pa eses versiones tan antigües.

> Dambos proyeutos son non oficiales, los lleva la comunidá y nun tán afiliaos a Microsoft. Son la única forma realista d'actualizar estos sistemes anguaño. Úsalos nun equipu que puedas reinstalar y caltién copies de seguridá. Inclusive con toles actualizaciones, estos sistemes operativos siguen siendo un riesgu pa la seguridá — aprefier un sistema soportáu siempre que puedas.

### Windows 7: habilitar actualizaciones pa «otros productos de Microsoft»

Nuna instalación nueva de Windows 7, la opción **«Get updates for other Microsoft products»** (Microsoft Update) falta dacuando, asina que Windows Update namás ufierta actualizaciones pa Windows. Pa restaurar el Microsoft Update completu:

1. Instala primero les actualizaciones previes. La forma más fácil ye executar **Legacy Update**, que detecta ya instala too lo que falta. A mano precisas `KB4474419` (soporte de firma de códigu SHA-2) y `KB4490628` (pila de serviciu), siguíos d'un reiniciu y de la última actualización de la pila de serviciu.
2. Instala **Office 2010** (cualquier edición). El so instalador pon l'axente más nuevu de **Microsoft Update**, que vuelve a rexistrar la fonte d'actualizaciones de «otros productos de Microsoft» que rique'l Windows 7 actual.
3. Reinicia y, n'Internet Explorer, visita <http://update.microsoft.com> y/o abre **Windows Update** y fai clic en **Find out more** xunto a *«Get updates for other Microsoft products»* pa aceptar. (Si la opción sigue escondida, abre una aplicación d'Office como Word una vegada y acepta l'avisu d'«actualizaciones encamentaes».)
4. Vai a **Windows Update → Change settings** y confirma que la seición *Microsoft Update* volvió apaecer.

> Si Office 2010 ta instaláu **enantes** d'executar Legacy Update, la comprobación d'actualizaciones pue colgase pa siempre — ye un problema documentáu de Legacy Update. Executa Legacy Update primero.

### Cómo buscar ya instalar actualizaciones, pasu a pasu

**Windows modernu (8.1 / 10 / 11) al que Windows Update sigue dando serviciu:**

1. Abre **Settings → Update & Security → Windows Update**.
2. Fai clic en **Check for updates** y dexa que baxe ya instale.
3. Pa software y controladores opcionales, fai clic en **View optional updates** (Windows 10/11) y escueyi lo que precisas.
4. Reinicia y repite hasta que nun haya más actualizaciones.

**Windows antiguu (2000 – 8.1) con Legacy Update:**

1. Descarga `LegacyUpdateSetup.exe` dende <https://legacyupdate.net/> y executalu nel equipu de destino (Windows 2000/XP pue precisar primero la solución documentada y la última versión d'Internet Explorer).
2. Acepta l'avisu d'instalar les **actualizaciones previes**. Legacy Update detecta ya instala too lo que falta y reinicia cuando fai falta.
3. Abre **Start → All Programs → Legacy Update** (o l'enllaz **Install Updates** del sitiu) pa cargar la páxina clásica de Windows Update.
4. Fai clic en **Express** pa instalar toles actualizaciones crítiques/de seguridá automáticamente, o en **Custom** pa escoyer actualizaciones, controladores y software opcional individualmente.
5. Revisa la llista, fai clic en **Install Updates**, acepta les llicencies cuando te les pidan y dexa que s'instale.
6. Reinicia cuando te lo pida y **repite la comprobación hasta qu'apaiga nenguna actualización nueva**. La primer comprobación en Vista/7 pue tardar 30–60 minutos — ten paciencia.

**Windows bien antiguu (95 / 98 / Me / NT 4.0) con Windows Update Restored:**

1. Nel equipu de destino abre `windowsupdaterestored.com` en **Internet Explorer 4.0–6.0**.
2. Sigue l'**Prerequisites Installer** del sitiu (o los pasos manuales); el sitiu carga'l control ActiveX orixinal de Microsoft que busca actualizaciones.
3. Usa **Windows Update** pa les actualizaciones del sistema y **Microsoft Update** pa Office y otros productos de Microsoft.
4. Instala les actualizaciones ufiertaes, reinicia y comprueba de nuevo hasta que la llista quede vacía.

**Alternativa manual:** casi toles actualizaciones tamién se publican nel **Catálogu de Microsoft Update** (<https://catalog.update.microsoft.com/>). Busca pol númberu KB, descarga'l `.msu`/`.cab` ya instálalu con doble clic (o con `wusa`/`dism`). Esta ye la forma más fiable d'actualizar un equipu que nun pue llegar a nengún serviciu d'actualización.

## Nucleos estendíos: executar software más modernu en Windows antiguu

Un **nucleu estendíu** (tamién llamáu *paquete d'estensión d'API*) ye un parche non oficial que añade **API de Windows más modernes a una instalación de Windows antigua**. En llugar d'emular Windows, modifica'l nucleu real y les DLL del sistema (`kernel32.dll`, `ntdll.dll`, `user32.dll`, `advapi32.dll`, …) pa que los programes escritos pa una versión posterior de Windows atopen les funciones que esperen y arranquen con normalidá. El resultáu ye una instalación de Windows xenuina y nativa que pue executar software d'una o más xeneraciones posteriores.

### Proyeutos principales

| Proyeutu | Sistema de destino | Añade compatibilidá pa | Notes |
|---|---|---|---|
| **KernelEx** | Windows 98 / Me | Windows 2000/XP applications | Open source; needs the `unicows` runtime. |
| **Windows 2000 Extended Kernel** (BlackWingCat) | Windows 2000 | Windows XP applications + modern software (VS2013 apps, Media Player 11, newer browsers) | Works on non-SSE2 CPUs; installed like a service pack. |
| **One-Core-API** (shorthorn-project) | Windows XP SP3, XP x64 SP2, Server 2003 SP2 | Vista/7/8/10 APIs — modern Chromium/Firefox, Steam, VS Code, .NET up to 4.8, DirectX 9–11 games via a `wined3d` wrapper, Office 2013/2016 | Based on ReactOS; actively developed. |
| **VxKex / VxKex NEXT** | Windows 7 (+ Server 2008 R2) | Windows 8/8.1/10/11-only applications (Chromium, Firefox, Blender, Python, VSCode, Spotify, …) | Per-application opt-in; patches each app's import table. |

### Ventayes

- **Rendimientu nativu y soporte completu de controladores.** L'aplicación funciona nel nucleu NT xenuino con controladores reales de hardware, asina que nun hai sobrecarga d'emulación de CPU nin capa de traducción de GPU/controladores.
- **Efectu en tol sistema.** Una vegada instaláu, *toos* los programes compatibles beneficiense — nun fai falta configurar cada aplicación (sacante VxKex, que ye por aplicación por diseñu).
- **Funciona en Windows real y yá con llicencia.** Caltiens la instalación, los ficherus y les llicencies que yá tienes.
- **A menudo ye l'únicu xeitu d'executar una aplicación moderna en hardware antiguu** (ex. Windows 2000 nun Pentium III ensin SSE2).

### Desventayes

- **Non oficial y ensin soporte.** Una actualización fallida pue dexar el sistema ensin arrancar; fai siempre una imaxe/copia de seguridá primero (los nucleos estendíos tamién puen entrar en conflictu con Windows Update).
- **Seguridá.** Estás executando códigu más nuevu y más complexo nun sistema operativu que yá nun recibe arreglos de seguridá — nun lo uses nun equipu espuestu a la rede.
- **Incompletu.** Namás s'implementa una parte de les API; dalgunes aplicaciones siguen fallando y puen faltar o ser inestables carauterístiques como la impresión, el DRM o l'aceleración de hardware.
- **L'antivirus / EDR pue marcalu como peligrosu**, y rompe la integridá de los ficherus del sistema (`sfc`/`dism` van quexase).
- **Trabayu de caltenimientu.** Tienes de siguir les versiones d'un proyeutu de terceros.

### Nucleu estendíu vs. Wine

| Aspeutu | Nucleu estendíu | Wine |
|---|---|---|
| What it is | Patch to a **real** Windows install | **Userspace compatibility layer** that re-implements the Windows API on Linux/macOS/BSD |
| Host OS | Genuine Windows (old) | Linux/macOS/BSD (no Windows needed) |
| Kernel | Modifies the actual NT kernel/DLLs | Runs as a normal process; no kernel changes |
| Performance | Native (near 100%) | Usually good, sometimes lower; no real kernel drivers |
| Drivers | **Full** real drivers work | **No** kernel drivers; hardware support is limited/emulated |
| Compatibility | Very high for the targeted Windows generation | Broad but hit-or-miss; often needs tweaks/`winetricks` |
| Risk | Can break/corrupt the OS; unsupported | Sandboxed; removing it is trivial |
| Licensing | Needs a Windows licence | Free (Wine is open source) |
| Typical use | Squeeze modern apps out of old Windows hardware | Run Windows games/apps on Linux without Windows |

En curtio: **Wine** ye la opción más segura y portátil si estás en Linux y puedes vivir con los vacíos de compatibilidá; un **nucleu estendíu** ye la opción de mayor rendimientu si quies caltener una instalación de Windows antigua xenuina y precisas controladores nativos.

### Cómo instalar (exemplos)

> Fai siempre una imaxe completa del discu (o una instantánea de la VM) enantes d'instalar cualquier nucleu estendíu. Fai estes instalaciones nuna instalación de Windows nueva.

**1. KernelEx (Windows 98 / Me)**

1. Instala primero'l **Unofficial Service Pack** y la biblioteca **`unicows`**.
2. Descarga l'instalador más reciente de KernelEx del proyeutu y executalu.
3. Reinicia. Depués fai clic derechu en cualquier `.exe` → **Properties → Compatibility** pa escoyer el mou de KernelEx que debe usar esi programa.

**2. Windows 2000 Extended Kernel (BlackWingCat)**

1. Instala **Windows 2000 SP4** + Update Rollup 1 y reinicia.
2. Descarga'l paquete **Extended Kernel** más reciente (v3.0e o posterior).
3. Executa l'instalador como Alministrador, acepta los avisos y **reinicia dos vegaes**.
4. Instala la biblioteca de Visual C++ 2013 que precisa pa executar aplicaciones modernes.

**3. One-Core-API (Windows XP / Server 2003)**

1. Instala **Windows XP SP3** (o XP x64 SP2 / Server 2003 SP2) con toles actualizaciones.
2. Habilita **System Restore** y crea un puntu de restauración primero.
3. Descarga la versión más reciente de **One-Core-API** (instalador `ocapi_*`).
4. Fai clic derechu nel instalador → **Run as administrator**.
5. Reinicia. Pa xuegos de DirectX 9+, copia les DLL `wined3d` incluyíes na carpeta del xuegu y pon el **mou de compatibilidá** de la aplicación en Windows Vista/7 cuando te lo pida.

**4. VxKex (Windows 7 / Server 2008 R2)**

1. Instala **Windows 7 SP1** coles actualizaciones de SHA-2 y (pa aplicaciones modernes) de Universal C Runtime.
2. Descarga y executa l'instalador de **VxKex** (Alministrador) y reinicia.
3. Fai clic derechu nel programa de destino → pestaña **Properties → VxKex** → marca **Enable VxKex for this program**.
4. Si l'aplicación entá se niega a arrancar, pon el so **mou de compatibilidá** en Windows 8/10 na mesma ventana de Properties (delles aplicaciones tamién precisan una falsificación de versión, configurada en **VxKex Global Settings**).

## Base de conocimientu / resolución de problemes

- **«Sentinel marked this request as rejected»** — el sistema anti-abusu de Microsoft bloqueó la petición pola reputación de la to IP. Espera 24–48 hores, usa una VPN o descarga a mano dende la páxina oficial. Esto **nun** ye un fallu de Mido — el caltenedor de Fido documenta'l mesmu comportamientu. Los argumentos `win10x64-esp`, `win10x64-es-mx`, `win11x64-esp` y `win11x64-es-mx` eviten esto recurriendo automáticamente a una copia d'archive.org.
- **Recurso d'archive.org** — les ISOs n'español dedicaes prueben primero la descarga oficial de Microsoft y, si Sentinel la refuga, baxen una copia idéntica d'`archive.org` (más lenta que la CDN de Microsoft). Mido reintenta y sigue automáticamente.
- **Windows 8.1 (`win81x64`) falla con HTTP 404** — Microsoft retiró la descarga automatizada de Windows 8.1. Usa meyor `win81x64-enterprise-eval`.
- **Windows 7 va lentu** — vien de `web.archive.org`, que ye muncho más lentu y menos fiable que la CDN de Microsoft. Mido reintenta automáticamente y sigue les descargues parciales.
- **ISOs de Windows 10 x86 (inglés, español)** — primero pruebase la descarga oficial; el recursu d'`archive.org` (compilaciones de Windows 10 22H2) pue ser más lentu que la CDN de Microsoft. Mido reintenta y sigue automáticamente.
- **ISOs de Windows 8.1 Enterprise n'español** — vienen d'`archive.org`, que pue ser más lentu que la CDN de Microsoft. Mido reintenta y sigue automáticamente.
- **ISO de Windows Server 2008 R2 n'español** — vien d'`archive.org`, que pue ser más lentu que la CDN de Microsoft. Mido reintenta y sigue automáticamente.
- **ISOs de Windows Server 2003 / Windows 2000 Server** — vienen d'`archive.org`, que pue ser más lentu que la CDN de Microsoft. Mido reintenta y sigue automáticamente.
- **ISOs de Windows Vista** — vienen d'`archive.org`, que pue ser más lentu que la CDN de Microsoft. Mido reintenta y sigue automáticamente.
- **Medios retro de Windows (`win30`, `win31`, `wfw31`, `win311`/`wfw311`, `win95`, `win98`, `winme`, `winnt31`, `winnt35`, `winnt351`, `winnt40`, `backoffice`, `winframe`, más los n'español `win30-espa`, `win31-espa`, `wfw31-espa`, `win311-espa`, `wfw311-espa`, `win95-espa`, `win98-espa`, `winme-espa`)** — vienen d'`archive.org`, que pue ser más lentu que la CDN de Microsoft. Dalgunes de les entraes más antigües vienen como imáxenes de disquete ZIP/RAR/7z en llugar d'ISOs. Mido reintenta y sigue automáticamente.
- **Enterprise/Server «nun hai enllaz de descarga»** — Microsoft cambia periódicamente les páxines del Evaluation Center. Abre un issue cola versión afeutada.

## Quies aforrar más tiempu?

Mira'l script `create-media.sh` en [Qvm-Create-Windows-Qube](https://github.com/ElliotKillick/qvm-create-windows-qube/tree/master/windows)! Anguaño inclúi Mido *y* un ficheru de respuestes pa ca descarga ufiertada.

## Cómo de seguru ye *de verdá*?

Mido ye un software razonablemente seguru. Aprovecha toa oportunidá pa reducir la superficie d'ataque. Los datos non fiables trátanse como tales, con pasos de validación afayaízos. Usa siempre la versión más alta posible de TLS (hasta TLS 1.3). Pues comprobar tu mesmu les propiedaes de seguridá nel script de shell, que ye tresparente.

- Nun usar navegador web (Chromium headless executando JavaScript) reduce la superficie d'ataque en *munchos* ordes de magnitú.
- Fuercia TLS 1.2 o TLS 1.3 (esti últimu cuando los servidores de Microsoft lo soporten).
- Compatible con POSIX sh y cambia automáticamente a un shell más seguru (Dash) si ta disponible.
- Fuercia HTTP/1.1 pa evitar los [fallos frecuentes de curl con HTTP/2 y HTTP/3](https://github.com/curl/curl/issues?q=is%3Aissue+label%3Acrash).
- Namás s'usen les funciones internes del shell pa la funcionalidá más crítica.
- Verifica les sumes SHA-256 de toles ISOs descargaes.

¿Entá hai fallos? Envuélvelo en plásticu de burbuyes: `bwrap --ro-bind /bin /bin --ro-bind /usr/bin /usr/bin --ro-bind /lib /lib --ro-bind /usr/lib /usr/lib --ro-bind /lib64 /lib64 --ro-bind /usr/lib64 /usr/lib64 --ro-bind /usr/share /usr/share --ro-bind /etc /etc --dev-bind /dev/null /dev/null --bind "$PWD" "$PWD" --ro-bind "$PWD/Mido.sh" "$PWD/Mido.sh" --unshare-all --share-net -- ./Mido.sh --help`

## Pruebes

```sh
sh -n Mido.sh          # syntax check
shellcheck -s sh Mido.sh
```

## Llicencia

Llicencia MIT - Copyright (C) 2024 Elliot Killick <contact@elliotkillick.com>
