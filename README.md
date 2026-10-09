<div align="center">
    <a href="https://github.com/BENDER35/Mido_2026">
        <img width="160" src="assets/logo.png" alt="Logo" />
    </a>
</div>

<h3 align="center">
    Mido 2026
</h3>

<p align="center">
    The <b>Secure</b> Microsoft Windows Downloader
</p>

<p align="center">
    <a href="README.md">English</a> · <a href="README.es.md">Español</a>
</p>

Mido is a secure and open source download client for Microsoft's (reverse engineered) proprietary downloading API! Downloads are sourced from **official** Microsoft servers and you only have to run one command to go from start to finish in no time!

Comes with advanced features like download resumption, SHA-256 checksum verification, retry logic, multilingual ISOs (English, Spanish and Mexican Spanish) and downloading many different Windows versions in a single command. Did I mention it's written in *pure* POSIX sh (w/ few coreutils) + curl so it will run anywhere (even on Windows with WSL or a Cygwin shell)? So robust, very minimalist!

> **2026 update:** Microsoft replaced their old HTML download API with a JSON
> `software-download-connector` API protected by an anti-abuse service
> (Microsoft Sentinel). Mido 2026 implements the new API and the required
> handshake, fixes several shell bugs, adds retry logic and adds Spanish
> language support. See [docs/TECHNICAL.md](docs/TECHNICAL.md) for the details.

#### ❌ Microsoft's Media Creation Tool (`mediacreationtool.exe` proprietary bloatware)

<p align="center">
    <img src="assets/bloatware1.png" alt="Microsoft's proprietary bloatware executable"></img>
    <br />
    <img src="assets/bloatware2.png" width="250px" alt="Microsoft's proprietary bloatware"></img>
    <img src="assets/bloatware3.png" width="200px" alt="Microsoft's bloatware"></img>
</p>

Bloated website: `https://www.microsoft.com/en-us/software-download/windows11`
- Mido provides the exact same downloads as this website (it uses the same API)

#### ✅ Mido (using the **same** official Microsoft servers; <img src="https://awesome.re/badge.svg" style="position: relative; top: 5px;"></img> open source software)

<p align="center">
    <img src="assets/demo.gif" width="400" alt="Project demo GIF"></img>
</p>

## Get Mido

Get [Mido.sh](https://raw.githubusercontent.com/BENDER35/Mido_2026/main/Mido.sh) by opening the link, right-clicking and then selecting "Save [Page] as..."

### Mac & Linux

You're done! Just open a terminal, give the file execution permissions (`chmod +x Mido.sh`), and run the script (as seen in the above GIF) to start using Mido.

### Windows

To run Mido on Windows, use WSL (Windows Subsystem for Linux). If you don't have it enabled already then search "Turn Windows features on or off" in the Start menu, open that, check the "Windows Subsystem for Linux" box, and click "OK". This is the best option.

Alternatively, install [Cygwin](https://www.cygwin.com/install.html) or [MSYS2](https://www.msys2.org/#installation) from their download pages, or in one command using WinGet:

```
winget install -e --id Cygwin.Cygwin
winget install -e --id MSYS2.MYS2
```

Both are POSIX emulation environments for Windows and you can use either one.

#### Windows Native Wrappers

Mido includes native Windows wrappers for convenience:

- **`Mido.bat`** - Batch file wrapper (run by double-clicking or from cmd)
- **`Mido.ps1`** - PowerShell wrapper (run from PowerShell)

Both wrappers automatically detect WSL and run `Mido.sh` through it, passing all arguments. Example usage:

```
Mido.bat win11x64
Mido.ps1 win10x64 win11x64
```

If WSL is not installed, the wrappers will prompt you to install it.

## Usage

```
./Mido.sh <windows_media>...
```

Examples:

```
./Mido.sh win11x64                       # Windows 11, English (US)
MIDO_LANG=es-ES ./Mido.sh win10x64       # Windows 10, Spanish (Spain)
MIDO_LANG=es-MX ./Mido.sh win11x64       # Windows 11, Mexican Spanish
./Mido.sh win7x64-ultimate win10x64      # Multiple ISOs in one command
./Mido.sh all                            # Every supported media
```

### Available media

| Argument | Description |
|---|---|
| `vista-x64-sp2` | Windows Vista SP2 x64 (English, sourced from archive.org) |
| `vista-x86-sp2` | Windows Vista SP2 x86 (English, sourced from archive.org) |
| `vista-es-x64-sp2` | Windows Vista SP2 x64 Spanish (sourced from archive.org) |
| `vista-es-x86-sp2` | Windows Vista SP2 x86 Spanish (sourced from archive.org) |
| `win7x64-ultimate` | Windows 7 Ultimate x64 (sourced from the Wayback Machine) |
| `win81x64` | Windows 8.1 x64 (**retired by Microsoft**, use the Enterprise Eval) |
| `win10x64` | Windows 10 x64 (multi-edition) |
| `win10x86` | Windows 10 x86 (32-bit) Spanish — Spain (`win10x86-esp`) or Mexico (`win10x86-es-mx`) |
| `win11x64` | Windows 11 x64 (multi-edition) |
| `win81x64-enterprise-eval` | Windows 8.1 Enterprise Evaluation |
| `win10x64-enterprise-eval` | Windows 10 Enterprise Evaluation |
| `win11x64-enterprise-eval` | Windows 11 Enterprise Evaluation |
| `win10x64-enterprise-ltsc-eval` | Windows 10 Enterprise LTSC Evaluation (most secure) |
| `win2008r2` | Windows Server 2008 R2 SP1 (x64, English) |
| `win2012r2-eval` | Windows Server 2012 R2 Evaluation |
| `win2016-eval` | Windows Server 2016 Evaluation |
| `win2019-eval` | Windows Server 2019 Evaluation |
| `win2022-eval` | Windows Server 2022 Evaluation |

## Language support

Set the `MIDO_LANG` environment variable to one of:

| Value | Language |
|---|---|
| `en-US` | English (United States) — default |
| `es-ES` | Spanish (Spain) |
| `es-MX` | Spanish (Mexico) |

Spanish ISOs are available for the **consumer** versions (`win10x64`, `win11x64`).
Additionally, Windows 10 32-bit (x86) ISOs in Spanish are available via archive.org
for both Spain (`win10x86-esp`) and Mexico (`win10x86-es-mx`).
Windows Vista SP2 ISOs in Spanish are also available via archive.org
(`vista-es-x64-sp2`, `vista-es-x86-sp2`).
Enterprise, Server and Evaluation media are English-only, as published by Microsoft.
Non-English ISOs are written with a locale suffix (e.g. `win11x64.es-MX.iso`) so
they never overwrite the English ones. Because Microsoft does not publish public
checksums for every localized release, localized ISOs may report
`NO KNOWN CHECKSUM (skipping verification)`.

## How does Mido work?

It interacts with Microsoft's proprietary downloading API to grab the latest
release of Windows and generate a fresh download link (valid for 24 hours). Then
it downloads the file, verifies its SHA-256 checksum and resumes partial
downloads automatically.

In detail:

1. Scrapes the official download page to obtain the *product edition ID* for the
   requested release.
2. Whitelists a random *session ID* and completes Microsoft's *Sentinel*
   anti-abuse handshake.
3. Queries the JSON API for the *SKU ID* matching the requested language.
4. Requests a fresh download link and downloads it to `<file>.PART`.
5. Verifies the SHA-256 checksum and renames the file to either `<file>` (OK) or
   `<file>.UNVERIFIED` (needs manual review).

See [docs/TECHNICAL.md](docs/TECHNICAL.md) for a full walk-through.

## What else can Mido do?

Other than the consumer versions of Windows like 11 and 10, it can also automatically download the latest Server (e.g. Windows Server 2022) and Enterprise editions of every Windows version all the way back to Windows 7 (or Server 2008 R2)!

Want a more secure and minimalist Windows installation out-of-the-box that's officially provided by Microsoft? Then download the LTSC version of Windows. It comes with way less bloat and supports Microsoft's ["Security"](https://learn.microsoft.com/en-us/windows/privacy/configure-windows-diagnostic-data-in-your-organization#diagnostic-data-settings) telemetry mode (plus it comes with long-term support).

## Knowledge base / troubleshooting

- **"Sentinel marked this request as rejected"** — Microsoft's anti-abuse system
  blocked the request based on your IP reputation. Wait 24–48 hours, use a VPN, or
  download manually from the official page. This is *not* a bug in Mido — the
  maintainer of Fido documents the same behaviour.
- **Windows 8.1 (`win81x64`) fails with HTTP 404** — Microsoft retired the
  automated Windows 8.1 download. Use `win81x64-enterprise-eval` instead.
- **Windows 7 is slow** — it is sourced from `web.archive.org`, which is much
  slower and less reliable than Microsoft's CDN. Mido retries automatically and
  resumes partial downloads.
- **Windows 10 x86 Spanish ISOs** — sourced from `archive.org` (Windows 10 22H2
  builds), which may be slower than Microsoft's CDN. Mido retries and resumes
  automatically.
- **Windows Vista ISOs** — sourced from `archive.org`, which may be slower than
  Microsoft's CDN. Mido retries and resumes automatically.
- **Enterprise/Server "no download link"** — Microsoft periodically changes the
  Evaluation Center pages. Please open an issue with the affected version.

## Want to save more time?

Check out the `create-media.sh` script in [Qvm-Create-Windows-Qube](https://github.com/ElliotKillick/qvm-create-windows-qube/tree/master/windows)! Now complete with Mido *and* an answer file to go with each provided download.

## How secure is it *really*?

Mido is reasonably secure software. Every chance to reduce attack surface is taken. Untrusted data is treated as such with proper validation steps. The highest possible version of TLS is always used (up to TLS 1.3). Easily verify security properties yourself in the transparent shell script.

- No web browser (headless Chromium running JavaScript) reduces the attack surface by *many* orders of magnitude.
- Force TLS 1.2 or TLS 1.3 (the latter when Microsoft servers support it).
- POSIX sh compatible and automatically switches to a more secure shell (Dash) if available.
- Force HTTP/1.1 to avoid frequent [curl HTTP/2 & HTTP/3 bugs](https://github.com/curl/curl/issues?q=is%3Aissue+label%3Acrash).
- Only shell builtins are used for the most critical functionality.
- Verify SHA-256 checksums of every downloaded ISO.

Still bugs? Wrap it in bubble wrap: `bwrap --ro-bind /bin /bin --ro-bind /usr/bin /usr/bin --ro-bind /lib /lib --ro-bind /usr/lib /usr/lib --ro-bind /lib64 /lib64 --ro-bind /usr/lib64 /usr/lib64 --ro-bind /usr/share /usr/share --ro-bind /etc /etc --dev-bind /dev/null /dev/null --bind "$PWD" "$PWD" --ro-bind "$PWD/Mido.sh" "$PWD/Mido.sh" --unshare-all --share-net -- ./Mido.sh --help`

## Testing

```sh
sh -n Mido.sh          # syntax check
shellcheck -s sh Mido.sh
```

## License

MIT License - Copyright (C) 2024 Elliot Killick <contact@elliotkillick.com>
