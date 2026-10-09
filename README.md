# Orbit Store Enhanced

### A modern, no-BS download manager for PS5.

Orbit runs on your PS5. Browse games, compare their available sources and formats, and download a single file directly to the console or an attached drive. Open the native TV app from your Games row, or pair a phone or computer to manage the same queue over your local network.

Start with **705 games** from Archive.org and Vikingfile. New games and corrected links arrive through catalogue updates. You choose which sources to enable and which storage to use.

This is a fork of [Orbit Store](https://github.com/saawant12/orbit-store-ps5) by saawant12, kept in sync with the upstream catalogue and extended with direct package installation from your own network shares and USB drives.

---

## Enhanced

Everything upstream does, plus the following.

### Streaming package installation

FPKG packages install straight to console storage. Bytes travel from the source through a loopback HTTP server into `sceAppInstUtilInstallByPackage` — nothing is written to a drive first.

Upstream downloads a package to a drive, then installs it, so a 90 GB game needs 180 GB free. Here it needs none. The installer pulls parallel 4 MiB ranges from a dedicated server on port 18841, independent of Orbit's own HTTP server.

### My SMB

Point Orbit at a network share and browse it like a file manager. Breadcrumbs, folders, packages, an install button on each one.

Shares are read with libsmb2 rather than curl, which can list an SMB directory but cannot read files from a server running `server min protocol = SMB2`. Reads are synchronous — the async credit model is a documented source of stalls.

### My USB

Drives attached to the console are enumerated automatically: `usb0-7`, `ext0-7` and `/data`. No manual registration. Unplug a drive and its entries disappear; take a network share offline and its entries stay, because the files are still there.

### Packages identified by their contents

Title ID, name and cover art are read from inside the `.pkg`, never guessed from the filename. Real collections name files three different ways:

```
UP1063-PPSA08832_00-RAIDENXMIKADOPS5-A0100-V0100.pkg
MARVEL'S WOLVERINE (PPSA03671) [v01.01][BP4.xx+] 94.7GB.pkg
EP9000-PPSA21567_00-0000000000000000.pkg
```

Three header variants are accepted: `\x7fFIH` for full packages, `\x7fLIH` for backports and patches, `\x7fCNT` for unwrapped ones. The content block can sit 65 GB into the file.

PS5 packages carry their name in `param.json`; PS4 packages carry it in `param.sfo`. Both are read.

### My Library reads the console

Installed games come from `/user/app` and the console's own `app.db`, not from ShadowMount — which only tracks images it manages, and reports nothing for packages installed through AppInstUtil.

Title names for PS4 games live only in the `tbl_contentinfo` table of a 552 KB SQLite file. Rather than embedding 9 MB of `sqlite3.c` for one table and two columns, there is a 225-line read-only B-tree reader. Every bound is checked; any failure means "name not found", never a crash.

Only `CUSA` and `PPSA` titles are listed. Homebrew payloads and system applications are filtered out.

### Reinstall guard

Installing a title that is already present is refused once with a clear message. Choose it again and it proceeds.

### Honest cancellation

Once a package reaches AppInstUtil, the console owns the installation and there is no cancel API for it. Orbit says so instead of marking the job cancelled and leaving the console installing in the background. Downloads that write to a drive cancel normally and delete their partial file.

---

## Installation

You need a PS5 that can run homebrew ELF payloads, a payload manager or ELF loader, internet access on the console, and enough writable storage.

**Native TV app.** Download `PPSA99177.ffpkg` from the [latest release](https://github.com/kingpoky/orbit-store-enhanced/releases/latest), copy it to `/data/homebrew/`, install it, then open Orbit from the Games row. This needs **kstuff and ShadowMountPlus**.

**Browser version.** Run `orbit_store.elf` through your loader and open Orbit from the Media tab.

Then:

1. **Choose your sources.** In the setup screen or **App settings → Download sources**, select Archive.org, Vikingfile, or both, and acknowledge the download notice. Your choices apply to both versions.
2. **Add your own.** For a network share, open **App settings → Folders**, enter an address such as `smb://192.168.0.5/media/games`, and scan it. USB drives appear on their own.
3. **Pick a download.** Open a game and select its download button to review **Download options**: **Source**, **Download using** and **Save to**. FPKG options stream to console storage and show no destination, because there is nothing to choose.
4. **Follow your queue.** Open Downloads on the TV or a paired device to check progress, pause or resume.

The TV app includes Orbit's download service and can start it through a compatible ELF loader on **port 9021**. You can also start `orbit_store.elf` yourself. After a reboot, start your jailbreak before opening Orbit.

Update checking is removed in this fork. Install a new build the same way you installed the first one.

## Controls

In the native TV app, use the D-pad or left stick to move, Cross to select, Circle to go back, and L1/R1 to switch tabs. Inside My SMB and My USB, Circle moves one folder up before it leaves the tab.

| Control | Action |
|---|---|
| D-pad / arrow keys | Move focus |
| Cross / Enter | Select |
| Circle / Escape | Back, or up one folder |
| L1 / R1 | Switch tabs |
| Touch / mouse | Select visible controls (browser version) |

The catalogue offers single-file **FFPFSC**, **exFAT** and **FPKG** options. Packages from your own shares and drives are always FPKG.

Orbit does **not** extract RAR/7z archives, launch games, or download in rest mode. Library actions beyond listing — mounting, copying, moving, scanning — still use ShadowMount.

## Building

```sh
npm run build
docker run --rm -v "$PWD:/work" orbit-build:0.1 payload
docker run --rm -v "$PWD:/work" -w /work/app orbit-app-build:0.1 make ffpkg
```

The payload builds with `-std=c11 -Wall -Wextra -Werror`. PS5-only code sits behind `ORBIT_INSTALL_ENGINE`, so the host build works without `libSceAppInstUtil`.

## Licence

Orbit Store is free software under the GNU General Public License, version 3 or later. This fork keeps that licence. Every release includes the complete source for its payload and native TV app, the corresponding sources of their copyleft components, third-party licence notices and rebuild instructions.

Upstream: [saawant12/orbit-store-ps5](https://github.com/saawant12/orbit-store-ps5).

---

## IMPORTANT: THIRD-PARTY CONTENT & DOWNLOAD DISCLAIMER

**Orbit Store is an independent download-management application. This project does not host, upload, mirror, or bundle the game files referenced by its catalogue.** File transfers happen directly between third-party providers and the user's selected device. This repository hosts Orbit's documentation and, when released, Orbit's own application files.

Catalogue entries reference publicly accessible third-party URLs. **Publicly accessible does not mean authorised, licensed, or free to redistribute.** Use Orbit only for material you have permission to obtain and use, in accordance with applicable law, relevant licences, and the provider's terms. Owning a game does not, by itself, establish permission to obtain any copy found online.

**Third-party files are outside this project's control.** Their hosts and uploaders control availability and contents. Orbit does not guarantee ownership, authenticity, completeness, safety, compatibility, or continued availability. Download completion, a matching file size, or a matching checksum is a technical result, not a licence or a guarantee that the file is safe to run.

All game names, artwork, trademarks, and other third-party materials belong to their respective owners. Orbit is **not affiliated with or endorsed by** Sony Interactive Entertainment, PlayStation, game publishers, or download providers. The app does not supply accounts, credentials, purchase entitlements, or permission to bypass access restrictions.
