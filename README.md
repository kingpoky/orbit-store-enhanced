# Orbit Store Enhanced

### A modern, no-BS download manager for PS5.

Orbit runs on your PS5. Browse games, compare their available sources and formats, and download a single file directly to the console or an attached drive. Open the native TV app from your Games row, or pair a phone or computer to manage the same queue over your local network.

Start with **705 games** from Archive.org and Vikingfile. You choose which sources to enable and which storage to use.

A fork of [Orbit Store](https://github.com/saawant12/orbit-store-ps5) by saawant12, extended to install packages from your own network shares and USB drives.

## Enhanced

- **Install without the free space.** Packages install straight to console storage. Nothing is saved to a drive first, so a 90 GB game no longer needs 180 GB free.
- **My SMB.** Point Orbit at a network share and browse it like a file manager. Install any package you find.
- **My USB.** Drives attached to the console are picked up automatically. No setup.
- **My Links.** Paste a direct download link and Orbit reads the package over the network — title, title ID and size come from the file itself. Nothing to type. Scan the QR code in the TV app to paste links from your phone.
- **Names and covers that are right.** Title, name and artwork are read from inside the package, whatever the file happens to be called.
- **A library that matches the console.** Installed games are read from the console itself, so everything you have installed shows up.
- **A warning before you reinstall.** Installing something you already have asks once before going ahead.
- **No update checks.** Install a new build the way you installed the first one.

## Installation

You need a PS5 that can run homebrew payloads, a payload manager, and enough free storage.

`orbit_store.elf` is the application — it holds your downloads, queue and catalogue, and keeps running after you close any front end.

1. **Start the payload.** Run `orbit_store.elf` from your payload manager. Open Orbit from the Media tab to use it in the browser.
2. **Optional: the TV app.** Copy `PPSA99177.ffpkg` to `/data/homebrew/`, install it, then open Orbit from your Games row. Needs **kstuff and ShadowMountPlus**. Start the payload first — if the app says it cannot reach Orbit, that is why.

After a console restart: jailbreak, then `orbit_store.elf`, then Orbit.

Both files are on the [latest release](https://github.com/kingpoky/orbit-store-enhanced/releases/latest).

## Licence

Free software under the GNU General Public License, version 3 or later, same as upstream. Each release ships with its full source.

---

**Orbit does not host, mirror or bundle any game files.** The catalogue points at publicly accessible third-party URLs, and transfers happen directly between those providers and your console. Publicly accessible does not mean authorised — use Orbit only for material you have permission to obtain. Orbit cannot guarantee the authenticity, safety or availability of files it does not control.

All game names, artwork and trademarks belong to their owners. Orbit is not affiliated with or endorsed by Sony Interactive Entertainment, PlayStation, game publishers or download providers.
