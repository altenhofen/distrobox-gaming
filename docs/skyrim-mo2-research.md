# Skyrim Special Edition / Anniversary Edition with MO2 and SKSE

Research snapshot: 2026-09-01. This is the implementation baseline for the
Steam edition of *The Elder Scrolls V: Skyrim Special Edition*. In SKSE
terminology, the up-to-date Steam build is the **Anniversary Edition** runtime;
it does not require that the user owns the separately sold Anniversary Upgrade.

## Current compatibility

- The current Steam runtime is **1.7.104** and its matching SKSE64 release is
  **2.3.1**. The [SKSE project download page](https://skse.silverlock.org/)
  explicitly says that current Steam users should select the AE build; the
  [SKSE64 file maintained by the SKSE Team](https://www.nexusmods.com/skyrimspecialedition/mods/30379?tab=files)
  identifies version 2.3.1 as compatible with Steam 1.7.104.
- Do not hard-code the old 1.6.1170 / SKSE 2.2.6 pair. It is now an archived
  Steam build. A recipe should instead obtain the current **Steam** SKSE64
  release at run time and check `SkyrimSE.exe`'s version before installing it.
- SKSE supports the latest Steam build only. Following a game update, SKSE
  plugins can need updates too; SKSE's own troubleshooting instruction is to
  remove `Data/SKSE/Plugins` while diagnosing an update-related launch failure.

## SKSE install layout

Install the contents of the SKSE archive into the same directory as
`SkyrimSE.exe` (the Steam game root), not only into MO2's virtual `Data`
directory. The archive's root loader/runtime files, including
`skse64_loader.exe`, `skse64_steam_loader.dll`, and (for this runtime)
`skse64_1_7_104.dll`, belong beside `SkyrimSE.exe`; the archive's `Data/`
contents merge into the game's `Data/` directory. Preserve the game's
Steam-managed files and do not replace `SkyrimSE.exe`. The Linux installer
project's [SKSE deployment log](https://github.com/Furglitch/modorganizer2-linux-installer/issues/308)
shows this exact root-plus-`Data` placement.

In MO2, add/select an executable that launches `skse64_loader.exe` from that
game root. Launch Skyrim through this MO2 entry, not Steam's vanilla executable,
so MO2's virtual filesystem and load order apply. The SKSE download page is the
authoritative source for the version pair and provides its official installation
video/documentation link: [SKSE downloads and guidance](https://skse.silverlock.org/).

## Linux / Proton approach

Use the maintained [MO2 Linux Installer (MO2-LINT)](https://github.com/Furglitch/modorganizer2-linux-installer), rather than treating a Windows MO2 archive as a standalone Wine program. Its project documents automated Proton setup, MO2 installation, Steam launch-option registration, NXM handling, game-specific workarounds, and optional script-extender installation; it lists Skyrim Special Edition as supported.

Actionable flow for a recipe:

1. Require Steam app **489830** to be installed inside the `gaming` box and
   launch it once under the selected Proton version so Steam creates its
   compatibility prefix.
2. Download the latest stable MO2-LINT release and run its installer inside the
   box. Select **Skyrim Special Edition / Steam** and enable its script-extender
   option only if it resolves to the verified current SKSE pair above. Otherwise
   install SKSE explicitly after the version check.
3. Launch app 489830 from Steam after setup: MO2-LINT wires Steam to open MO2.
   From MO2, select `SKSE` / `skse64_loader.exe` and run it.

The project's [post-install instructions](https://github.com/Furglitch/modorganizer2-linux-installer/wiki/Post%E2%80%90Install-Instructions)
say that Steam's launch options must retain `%command%`. If MO2 is deliberately
stored outside the Steam-accessible home path (for example on an external game
drive), add a mount for it, e.g.:

```sh
STEAM_COMPAT_MOUNTS="/path/to/mo2-instance" %command%
```

That same source advises launching through Steam rather than invoking MO2
outside Steam, because Steam supplies the Proton environment. A distrobox
recipe should therefore render a host launcher that enters the box and starts
the Steam app, rather than directly running `ModOrganizer.exe` with Wine.

## Recipe constraints

- Keep the recipe opt-in: it changes a live Steam game directory and Proton
  prefix, unlike normal package installation.
- Store the MO2 instance, downloads, mods, and profiles under a configurable
  `dg_` root that is writable and visible to Steam/Proton. If it is outside the
  box home, configure `STEAM_COMPAT_MOUNTS` rather than using a maintainer-local
  path.
- Update the version check whenever Bethesda ships a new runtime; SKSE announced
  an incoming Skyrim update on 2026-08-14, so a pinned recipe will become stale.
