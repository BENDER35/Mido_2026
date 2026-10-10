# Mido 2026 — Technical documentation

<p align="center">
    <a href="TECHNICAL.md">English</a> · <a href="TECHNICAL.es.md">Español</a> ·
    <a href="../README.md">README</a>
</p>

This document explains how `Mido.sh` works internally: the Microsoft endpoints it
talks to, the anti-abuse handshake, the download engine, integrity verification,
language support and the shell conventions used. It is aimed at maintainers and
anyone who wants to audit the script.

## Table of contents

1. [Requirements](#requirements)
2. [Repository layout](#repository-layout)
3. [High-level flow](#high-level-flow)
4. [Data sources](#data-sources)
5. [The consumer JSON API](#the-consumer-json-api)
6. [The Sentinel handshake](#the-sentinel-handshake)
7. [Language support](#language-support)
8. [Download engine (`scurl_file`)](#download-engine-scurl_file)
9. [Integrity verification](#integrity-verification)
10. [Error handling and exit codes](#error-handling-and-exit-codes)
11. [Constants and configuration](#constants-and-configuration)
12. [Security properties](#security-properties)
13. [Troubleshooting](#troubleshooting)
14. [Testing](#testing)
15. [Maintenance guide](#maintenance-guide)

## Requirements

- A POSIX shell (`sh`; on Debian/Ubuntu `dash` is preferred and the script
  re-executes itself under Dash when available).
- `curl` (with TLS 1.3 support for the best experience).
- Coreutils: `grep`, `sed`, `tr`, `cut`, `head`, `tail`, `sha256sum`, `mv`,
  `dirname`, `fold`, `kill`, `date`.
- Optional: `uuidgen` on systems without `/proc/sys/kernel/random/uuid`.

## Repository layout

```
.
├── Mido.sh               # the entire program
├── Mido.bat              # Windows batch wrapper (runs Mido.sh via WSL)
├── Mido.ps1              # Windows PowerShell wrapper (runs Mido.sh via WSL)
├── README.md             # user documentation (English)
├── README.es.md          # user documentation (Spanish)
├── docs/
│   ├── TECHNICAL.md      # this file
│   └── TECHNICAL.es.md   # Spanish translation
├── assets/               # logo, demo GIF, screenshots
└── LICENSE               # MIT
```

## High-level flow

```
parse_args "$@"
   └─ validates media names, expands "all", removes duplicates
download_media
   └─ for each media: dispatch to the correct download function
verify_media
   └─ verify every *.UNVERIFIED file, rename to final name
ending_summary
   └─ report failures and choose the process exit code
```

`set -ef` is enabled just before `parse_args`, so any unhandled command failure
aborts the script and the `EXIT` trap (`handle_exit`) warns about partial files.
`IFS` is forced to a single space, which is why ISO filenames cannot contain
spaces.

## Data sources

Different media families are fetched in different ways:

| Media family | Source | Auth |
|---|---|---|
| `win10x64`, `win11x64` | Consumer JSON API | Sentinel handshake |
| `win10x64-esp`, `win10x64-es-mx`, `win11x64-esp`, `win11x64-es-mx` | Consumer JSON API first, `archive.org` fallback | Sentinel handshake (official) |
| `win10x86` (x86 English and Spanish), `win10x86-esp`, `win10x86-es-mx` | Consumer JSON API first, `archive.org` fallback | Sentinel handshake (official) |
| `win7x64-ultimate-esp`, `win7x64-ultimate-es-mx` | Consumer JSON API first, `archive.org` fallback | Sentinel handshake (official) |
| `win81x64` | Consumer JSON API | Retired (HTTP 404) |
| `win81x64-ent-32-espa`, `win81x64-ent-64-esp` | `archive.org` snapshots | none |
| `win2000-advanced-server`, `win2000-advanced-server-espa` | `archive.org` snapshots | none |
| `win2008r2-espa` | `archive.org` snapshot of the official Spanish eval ISO | none |
| `win2008-server-x64`, `win2008-server-x64-espa`, `win2008-server-x86`, `win2008-server-x86-espa` | `archive.org` snapshots (official SP2 AIO ISOs) | none |
| `win2003-server`, `win2003-server-espa`, `win2003-server-x64`, `win2003-server-x64-espa`, `win2000-server`, `win2000-server-espa` | `archive.org` snapshots (legacy retail/eval ISOs) | none |
| `win2000-datacenter`, `win2000-datacenter-sp4` | `archive.org` snapshots (Windows 2000 Datacenter Server, RTM OEM with SP1 and SP4) | none |
| `win2000-pro`, `win2000-pro-espa` | `archive.org` snapshots (Windows 2000 Professional) | none |
| `win2000-pro-oem`, `win2000-pro-retail`, `win2000-pro-oem-espa`, `win2000-pro-retail-espa` | `archive.org` snapshots (Windows 2000 Professional, OEM/Retail channels, English and Spanish) | none |
| `win-xp-pro`, `win-xp-pro-espa`, `win-xp-pro-32-espa` | `archive.org` snapshots (Windows XP Professional, English and Spanish) | none |
| `win-xp-home`, `win-xp-home-espa`, `win-xp-home-oem`, `win-xp-home-retail`, `win-xp-home-oem-espa`, `win-xp-home-retail-espa`, `win-xp-pro-oem`, `win-xp-pro-retail`, `win-xp-pro-oem-espa`, `win-xp-pro-retail-espa` | `archive.org` snapshots (Windows XP Home/Professional, OEM/Retail channels, English and Spanish) | none |
| `win-xp-pro-64` | `archive.org` snapshot (Windows XP Professional x64 Edition SP2, English; Microsoft shipped x64 as English/Japanese with MUI language packs) | none |
| `win7x64-pro`, `win7x64-pro-esp`, `win7x64-pro-es-mx` | `archive.org` snapshots (Windows 7 Professional) | none |
| `win7x86-pro`, `win7x86-pro-esp`, `win7x86-pro-es-mx` | `archive.org` snapshots (Windows 7 Professional) | none |
| `win7x64-homepremium`, `win7x64-homepremium-esp`, `win7x64-homepremium-es-mx` | `archive.org` snapshots (Windows 7 Home Premium) | none |
| `win7x86-homepremium`, `win7x86-homepremium-esp`, `win7x86-homepremium-es-mx` | `archive.org` snapshots (Windows 7 Home Premium) | none |
| `win7x64-enterprise`, `win7x64-enterprise-esp`, `win7x64-enterprise-es-mx` | `archive.org` snapshots (Windows 7 Enterprise) | none |
| `win7x86-enterprise`, `win7x86-enterprise-esp`, `win7x86-enterprise-es-mx` | `archive.org` snapshots (Windows 7 Enterprise) | none |
| `win7x64-sp1`, `win7x86-sp1` | `archive.org` snapshots (Windows 7 SP1) | none |
| `win81x64-pro`, `win81x64-pro-esp`, `win81x64-pro-es-mx` | Consumer JSON API first, `archive.org` fallback | Sentinel handshake (official) |
| `win10x64-enterprise-eval`, `win10x86-enterprise-eval`, `win11x64-enterprise-eval`, `win10x64-enterprise-ltsc-eval`, `win11x64-enterprise-ltsc-eval` | `archive.org` snapshots | none |
| `win11x64-iot-enterprise-ltsc-eval` | Direct `software-static.download.prss.microsoft.com` URL | none |
| `win2012r2-eval` … `win2025-eval`, `win2012r2-essentials-eval`, `win2016-essentials-eval`, `win2019-essentials-eval`, `hyperv2012-eval`, `hyperv2012r2-eval`, `hyperv2016-eval`, `hyperv2019-eval` | Evaluation Center HTML → `go.microsoft.com/fwlink` redirect | none |
| `win81x64-enterprise-eval`, `win2008r2` | Direct `download.microsoft.com` URL | none |
| `win2022-enterprise-64`, `win2022-datacenter-64` | `archive.org` snapshots (64-bit only) | none |
| `win2022-enterprise-64-esp`, `win2022-datacenter-64-esp` | `archive.org` snapshots Spanish (64-bit only) | none |
| `win2025-enterprise-64`, `win2025-datacenter-64` | `archive.org` snapshots (64-bit only) | none |
| `win2025-enterprise-64-esp`, `win2025-datacenter-64-esp` | `archive.org` snapshots Spanish (64-bit only) | none |
| `win7x64-ultimate` | Consumer JSON API first, `archive.org` fallback | Sentinel handshake |
| `win7x86-ultimate`, `win7x86-ultimate-esp`, `win7x86-ultimate-es-mx` | `archive.org` snapshots (Windows 7 Ultimate) | none |
| `vista_x64_sp1`, `vista_x86_sp1`, `vista_es_x64_sp1`, `vista_es_x86_sp1` | `archive.org` snapshots | none |
| `vista_x64_sp2`, `vista_x86_sp2`, `vista_es_x64_sp2`, `vista_es_x86_sp2` | `archive.org` snapshots | none |
| `win311`, `win95`, `win98`, `winme`, `winnt31`, `winnt35`, `winnt351`, `winnt40`, `win95-espa`, `win98-espa`, `winme-espa`, `backoffice`, `winframe` | `archive.org` snapshots (retro retail/OEM ISOs; 95/98/ME also in Spanish; NT 3.1/3.5/3.51 Workstation, BackOffice SBS 4.0 and Citrix WinFrame 1.6) | none |

> Windows 10/11 Enterprise and LTSC evaluations were migrated from the
> (unreliable) Evaluation Center HTML to archived `archive.org` ISOs. The
> remaining `enterprise_eval_download` path is now used only by the Windows
> Server, Server Essentials and Hyper-V evaluations (`win2012r2-eval` …
> `win2025-eval` and the Essentials/Hyper-V entries).
>
> `win10x86-enterprise-eval` is the 32-bit (x86) counterpart of
> `win10x64-enterprise-eval`: it points to the official Windows 10 Enterprise
> LTSC 21H2 x86 evaluation ISO archived on archive.org (item
> `Win10-EnterpriseLTSC-21H2`).

## The consumer JSON API

Microsoft retired the old HTML endpoint
(`/software-download/contentinclude/html`) which now returns `404`. The current
API lives under:

```
https://www.microsoft.com/software-download-connector/api
```

Two endpoints are used:

1. `getskuinformationbyproductedition`
   `?profile=<PROFILE_ID>&productEditionId=<id>&SKU=undefined&friendlyFileName=undefined&Locale=<locale>&sessionID=<sid>`
   Returns the list of SKUs (one per language) for a product edition. Example
   fragment:

   ```json
   {"Skus":[
     {"Id":"27160","Language":"English"},
     {"Id":"27129","Language":"Spanish"},
     {"Id":"27130","Language":"Spanish (Mexico)"}
   ]}
   ```

2. `GetProductDownloadLinksBySku`
   `?profile=<PROFILE_ID>&productEditionId=undefined&SKU=<sku>&friendlyFileName=undefined&Locale=<locale>&sessionID=<sid>`
   Returns fresh download URIs, one per architecture:

   ```json
   {"ProductDownloadOptions":[
     {"DownloadType":2,"Uri":"https://...arm64.iso?t=..."},
     {"DownloadType":1,"Uri":"https://...x64.iso?t=..."}
   ]}
   ```

   `DownloadType` `1` is x64 (`2` is arm64). URIs are URL-encoded and `&` may
   appear as `\u0026` or `&amp;`, so they are decoded before use. Links are valid
   for **24 hours**.

The `productEditionId` is scraped from the human-facing download page
(`https://www.microsoft.com/<locale>/software-download/windows<N>[ISO]`) by
matching:

```
<option value="[0-9]+">Windows
```

This is why Mido always fetches the *latest* release without a hard-coded
release table (unlike Fido, which maintains one).

## The Sentinel handshake

`GetProductDownloadLinksBySku` is protected by Microsoft's anti-abuse service
(*Sentinel*). Without the handshake the response contains:

```json
{"Errors":[{"Key":"ErrorSettings.SentinelReject",
            "Value":"Sentinel marked this request as rejected.","Type":8}]}
```

Mido performs the same sequence as the Fido project:

1. Generate a random `session_id` (`/proc/sys/kernel/random/uuid` or `uuidgen`).
2. `GET https://vlscppe.microsoft.com/tags?org_id=<ORG_ID>&session_id=<sid>`
   to whitelist the session.
3. `GET https://ov-df.microsoft.com/mdt.js?instanceId=<INSTANCE_ID>&PageId=si&session_id=<sid>`
   and extract the `w` and `rticks` values.
4. `GET https://ov-df.microsoft.com/?session_id=<sid>&CustomerId=<INSTANCE_ID>&PageId=si&w=<w>&mdt=<epoch_ms>&rticks=<rticks>`.

**Important:** Sentinel is a *server-side, IP-reputation* check. It is not a
crash or a bug in Mido. Even the Fido maintainer documents that some IP ranges
are rejected while others succeed. Mido therefore prints an actionable message
instead of a raw error. There is no client-side workaround; the options are to
wait (24–48 h), use a different network/VPN, or download manually.

## Language support

`MIDO_LANG` (default `en-US`) accepts `en-US`, `es-ES` and `es-MX`. It is mapped
to the language name used by the API by `locale_to_api_language`:

| `MIDO_LANG` | API language |
|---|---|
| `en-US` | `English` |
| `es-ES` | `Spanish` |
| `es-MX` | `Spanish (Mexico)` |

The SKU lookup uses the trailing comma in `"Language":"<name>",` so that
`Spanish` does not accidentally match `Spanish (Mexico)`.

Output filenames for non-default languages get a locale suffix via
`localized_media` (`win11x64.iso` → `win11x64.es-MX.iso`) so downloads in
different languages never overwrite each other. Because Microsoft publishes
checksums only for the English ISOs, localized files hit the
`NO KNOWN CHECKSUM (skipping verification)` path (see below).

Besides `MIDO_LANG`, dedicated arguments exist for Windows 10/11 x64 in Spanish:
`win10x64-esp`, `win10x64-es-mx`, `win11x64-esp` and `win11x64-es-mx`. Each one
uses `consumer_download_or_archive`: it first tries Microsoft's official
download (with the matching locale) and, if the request fails — usually because
the *Sentinel* anti-abuse service rejects it based on IP reputation — it removes
the `.PART` left by the official attempt (so two different builds are never mixed)
and downloads an identical copy archived on `archive.org`. The fallbacks point to
22H2 builds (Windows 10) and 23H2 builds (Windows 11) in `es-ES`/`es-MX`.

The consumer JSON API also exposes 32-bit (x86) Windows 10 SKUs, so `win10x86`
tries Microsoft's official x86 download first and falls back to the archive.org
snapshot `Win10_22H2_English_x32v1`; `win10x86-esp` and `win10x86-es-mx` do the
same for Spain and Mexico. Windows 8.1
Enterprise Spanish ISOs are likewise archived on archive.org
(`win81x64-ent-32-espa`, `win81x64-ent-64-esp`). A Spanish evaluation ISO of
Windows Server 2008 R2 SP1 (`win2008r2-espa`) is also archived there, as are
Windows Server 2008 (non-R2) SP2 (`win2008-server-x64`, `win2008-server-x64-espa`,
`win2008-server-x86`, `win2008-server-x86-espa`), Windows Server 2003 and Windows
2000 Server SP4 in English and Spanish. Server
2003 is available as 32-bit Enterprise (`win2003-server`, `win2003-server-espa`)
and as 64-bit R2 Enterprise x64 SP2 (`win2003-server-x64`,
`win2003-server-x64-espa`); Windows 2000 Server SP4 is 32-bit (`win2000-server`,
`win2000-server-espa`), and Windows 2000 Datacenter Server is available as the RTM
OEM release with SP1 (`win2000-datacenter`) and as SP4 (`win2000-datacenter-sp4`).
These are not covered by the Microsoft API and have no
published checksums.

Windows Vista SP1 ISOs in English and Spanish are also sourced from archive.org
(`vista_x64_sp1`, `vista_x86_sp1`, `vista_es_x64_sp1`, `vista_es_x86_sp1`).
Windows Vista SP2 ISOs in English and Spanish are also sourced from archive.org
(`vista_x64_sp2`, `vista_x86_sp2`, `vista_es_x64_sp2`, `vista_es_x86_sp2`).
These are not covered by the Microsoft API and have no published checksums.

Windows 7 SP1 ISOs are also sourced from `archive.org` and are not covered by the Microsoft API.
Windows 2000 Professional, Windows XP Professional, Windows XP Home Edition and
Windows 7 Professional ISOs are also sourced from `archive.org` and are not covered by the Microsoft API.
Windows 2000 Professional and Windows XP additionally cover the original **OEM**
and **Retail** (FPP) license channels in English and Spanish
(`win2000-pro-oem`, `win2000-pro-retail`, `win2000-pro-oem-espa`,
`win2000-pro-retail-espa`, `win-xp-home-oem`, `win-xp-home-retail`,
`win-xp-home-oem-espa`, `win-xp-home-retail-espa`, `win-xp-pro-oem`,
`win-xp-pro-retail`, `win-xp-pro-oem-espa`, `win-xp-pro-retail-espa`); Windows
XP Home Edition and Windows XP Professional are available in English and Spanish
(`win-xp-home`, `win-xp-home-espa`, `win-xp-pro`, `win-xp-pro-espa`,
`win-xp-pro-32-espa`).
These have no published checksums.

> **Mexican Spanish (`es-MX`) for legacy Windows:** The Windows 2000 and Windows
> XP era shipped a **single Spanish build** for all Spanish-speaking regions;
> Microsoft did not publish a separate `es-MX` ISO for these versions. The `es-MX`
> locale is therefore only implemented for Windows 7, Windows 8.1, Windows 10 and
> Windows 11, for which Microsoft localized Spain (`es-ES`) and Mexico (`es-MX`)
> separately.

Windows 8.1 Pro ISOs are also available via the Consumer JSON API first, with
`archive.org` fallback for Spanish variants (`win81x64-pro-esp`, `win81x64-pro-es-mx`).
> Only the **consumer** editions expose localized SKUs through this API.
> Enterprise/Server evaluation media is English-only, as published by Microsoft,
> so `enterprise_eval_download` intentionally stays on `en-US`.

The retro releases (`win311`, `win95`, `win98`, `winme`, `winnt31`, `winnt35`,
`winnt351`, `winnt40`, `backoffice`, `winframe`) are sourced directly from
`archive.org` snapshots (Windows for Workgroups 3.11, Windows 95, Windows 98
Second Edition, Windows Millennium Edition, Windows NT 3.1, 3.5 and 3.51
Workstation, Windows NT 4.0 Workstation, as well as Microsoft BackOffice Small
Business Server 4.0 and Citrix WinFrame 1.6, the Windows NT 3.51 Terminal Server
Edition).
Windows 95, Windows 98 Second Edition and Windows Millennium Edition also have
Spanish snapshots (`win95-espa`, `win98-espa`, `winme-espa`); the remaining
retro releases are English-only.
They are not covered by the Microsoft API and have no published checksums, so
they go through the `NO KNOWN CHECKSUM (skipping verification)` path.

> **Note on product activation and NewShell:** None of the pre-XP media have a
> product activation mechanism (Microsoft introduced activation with Windows
> XP/Office XP in 2001). The telephone activation system was retired on
> 3 December 2025 and replaced by the online Product Activation Portal; see the
> README for the step-by-step instructions. The *NewShell* / Shell Technology
> Preview desktop patch applies only to Windows NT 3.51 (and the leaked NT 3.5
> build 854 preview); it is documented with download links in the README.

## Windows Update on unsupported Windows

Mido only *downloads* media; patching the installed system is a separate
problem. Microsoft has retired the update services that older Windows depends
on, so the README documents the community revival projects and the
step-by-step installation of updates. In short:

- **Vista and older (Server 2008 and older):** the classic Windows Update /
  Microsoft Update service is permanently shut down.
- **Windows 7 / Server 2008 R2:** Microsoft Update for "other Microsoft
  products" is closed. Install **Office 2010** to install the newer Microsoft
  Update agent and re-enable the full update list, and install the SHA-2
  prerequisites (`KB4474419`, `KB4490628`).
- **All unsupported versions:** use **Legacy Update**
  (<https://legacyupdate.net/>) for Windows 2000–11 and **Windows Update
  Restored** (<https://windowsupdaterestored.com/>, requires IE 4.0–6.0) for
  Windows 95/98/Me/NT 4.0. The Microsoft Update Catalog
  (<https://catalog.update.microsoft.com/>) is the manual fallback.

See the README section *"Windows Update on unsupported Windows"* for the
detailed, copy-pasteable instructions.

## Download engine (`scurl_file`)

Every media eventually calls `scurl_file <out_file> <tls_version> <url>`:

- `--progress-bar` — simple progress output.
- `--location` — follow redirects (Microsoft moves download endpoints often).
- `--output <file>.PART` — download to a temporary part file.
- `--continue-at -` — resume partial downloads automatically.
- `--max-filesize 10G` — reject absurd responses.
- `--fail` — fail on HTTP ≥ 400.
- `--proto =https` — only HTTPS.
- `--tlsv<version>` — force TLS 1.2 or 1.3.
- `--http1.1` — avoid known curl HTTP/2 and HTTP/3 bugs.
- `--retry 5 --retry-delay 5 --retry-connrefused` — transient network recovery.
- `--speed-limit 1024 --speed-time 30` — abort a download that stays below
  1 KiB/s for 30 s (useful for the flaky Wayback Machine source).

On failure the part file is renamed `<file>.UNVERIFIED.part` style handling is
avoided: the `.PART` file is kept for resumption and `handle_curl_error` is
called. Successful downloads are renamed to `<file>.UNVERIFIED`, awaiting
verification.

## Integrity verification

`verify_media` reads an embedded `sha256sums` table (media name → SHA-256). For
each requested media that produced a `.UNVERIFIED` file it:

1. Computes the SHA-256 of the file and sanity-checks it is 64 hex characters.
2. Looks up the expected checksum for that exact filename.
3. If no checksum is on record (typically a localized ISO), prints
   `NO KNOWN CHECKSUM (skipping verification)` and renames to the final name.
4. If the checksum matches, prints `OK` and renames to the final name.
5. If it does not match, leaves `<file>.UNVERIFIED` and records a failure.

Checksums for consumer releases are **not automatically updated** when Microsoft
ships a new build; a mismatch can mean a newer release, corruption, or
tampering. When this happens Mido prints manual verification instructions
(search the hash on DuckDuckGo/onion). This is a deliberate trade-off to keep
the script maintenance-free.

The Windows 7 checksum is immutable: that ISO was purged from Microsoft servers
and the Internet Archive copy is validated against the pre-purge checksum.

## Error handling and exit codes

`handle_curl_error` maps `curl` exit statuses to human-readable messages and
returns a *fatal error action* (`2`) for conditions that should abort the whole
run (e.g. out of memory). The default numeric pattern intentionally matches the
POSIX range `1–125`:

```sh
[0-9] | [0-9][0-9] | 1[0-1][0-9] | 12[0-5])
```

(An earlier version used an arithmetic expansion inside the `case` pattern that
only ever matched `error_code=1`; this has been fixed.)

Process exit codes (see `ending_summary`):

| Code | Meaning |
|---|---|
| 0 | Success |
| 1 | Argument parsing / configuration error |
| 2 | Fatal runtime error |
| 3 | One or more downloads failed |
| 4 | One or more verifications failed |
| 5 | At least one download **and** one verification failed |

The `EXIT` trap (`handle_exit`) runs on interruption and warns that `.PART` or
`.UNVERIFIED` files may remain.

## Constants and configuration

| Variable | Default | Purpose |
|---|---|---|
| `MIDO_LANG` | `en-US` | Download language |
| `ORG_ID` | `y6jn8c31` | Sentinel org id |
| `PROFILE_ID` | `606624d44113` | Consumer API profile id |
| `INSTANCE_ID` | `560dc9f3-1aa5-4a2f-b63c-9e18f8d0e175` | `ov-df` instance id |
| `API_BASE` | `.../software-download-connector/api` | Consumer API base |
| `DEBUG` | unset | `set -x` tracing |
| `VERBOSE` | unset | Print edition/SKU ids |

These values are copied from the Fido project and may need updating if Microsoft
rotates them.

## Security properties

- No browser, no JavaScript engine: this removes a huge attack surface compared
  to driving a headless browser.
- HTTPS only (`--proto =https`) with the highest available TLS version.
- `--http1.1` avoids crashes in curl's HTTP/2 and HTTP/3 implementations.
- Only shell builtins are used for the most security-critical logic; helper
  programs are used with fixed, validated arguments.
- All untrusted server output is filtered (`tr -cd '[:alnum:]...'`, length caps
  via `head -c`) before being used in URLs or filenames, mitigating HTTP
  parameter injection.
- SHA-256 verification of every downloaded ISO.
- The `all` argument can be combined with other arguments without aborting the
  loop, and duplicates are removed, so a single run cannot accidentally download
  the same large ISO twice.

## Troubleshooting

| Symptom | Cause | Action |
|---|---|---|
| `Sentinel marked this request as rejected` | IP reputation block | Wait 24–48 h, use a VPN, or download manually. The dedicated Spanish arguments (`win10x64-esp`, `win10x64-es-mx`, `win11x64-esp`, `win11x64-es-mx`) fall back to archive.org automatically |
| `715-123130` | IP banned by Microsoft | Same as above (the dedicated Spanish arguments fall back to archive.org) |
| `win81x64` → HTTP 404 | Microsoft retired Windows 8.1 automation | Use `win81x64-enterprise-eval` |
| Windows 7 very slow | Wayback Machine throttling | Let `--retry`/`--continue-at` work; be patient |
| archive.org fallback slow (Spanish Win10/11) | `archive.org` throttling | Let `--retry`/`--continue-at` work; resumes automatically |
| Windows 10 x86 (English/Spanish) very slow | `archive.org` throttling | Let `--retry`/`--continue-at` work; be patient |
| Windows 8.1 Enterprise Spanish very slow | `archive.org` throttling | Let `--retry`/`--continue-at` work; be patient |
| Windows Server 2008 R2 Spanish very slow | `archive.org` throttling | Let `--retry`/`--continue-at` work; be patient |
| Windows Server 2003 / 2000 Server very slow | `archive.org` throttling | Let `--retry`/`--continue-at` work; be patient |
| Windows Vista ISOs very slow | `archive.org` throttling | Let `--retry`/`--continue-at` work; be patient |
| `NO KNOWN CHECKSUM` | Localized ISO without a published hash | Verify manually if you wish |
| Enterprise/Server `no download link` | Evaluation Center page changed | Open an issue |

## Testing

```sh
sh -n Mido.sh                 # POSIX syntax check
shellcheck -s sh Mido.sh      # static analysis
```

Functional checks that do not require a successful download:

```sh
./Mido.sh --help
MIDO_LANG=fr-FR ./Mido.sh win11x64    # rejected with exit 1
./Mido.sh win81x64                    # prints the "retired" message, exit 3
VERBOSE=1 MIDO_LANG=es-MX ./Mido.sh win10x64   # shows edition id + es-MX SKU
```

## Maintenance guide

**Update a checksum.** Edit the `sha256sums` heredoc in `verify_media` after
verifying the new hash through an independent channel.

**Add a language.** Add the locale to the `case` in `MIDO_LANG` validation and a
branch to `locale_to_api_language`; `localized_media` then works automatically.

**Add a media.** Add a `readonly` variable near the other media names, a branch
in `parse_args` (including the `all` list), a `case` in `download_media`, an
entry in `usage()`, and, if applicable, a checksum line. If the media uses the
Microsoft-first-then-archive.org fallback, also add an entry in
`archive_fallback_url()`.

**Renew the Sentinel constants.** Update `ORG_ID`/`PROFILE_ID`/`INSTANCE_ID`
from a current Fido release if Microsoft rotates them.

## License

MIT License — Copyright (C) 2024 Elliot Killick <contact@elliotkillick.com>.
