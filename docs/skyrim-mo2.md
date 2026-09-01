# Skyrim SE / Anniversary Edition: Mod Organizer 2 + SKSE

This opt-in recipe prepares the current Steam **Skyrim Special Edition** build
(appid `489830`) for **Mod Organizer 2** and **SKSE64** under Proton. It treats
the modern Steam runtime as “AE” in SKSE terminology; you do not need to own the
separate Anniversary Upgrade.

## Before running it

1. Install Skyrim Special Edition inside Steam in the `gaming` distrobox.
2. In Steam, force a current Proton version and launch vanilla Skyrim once. It
   must create the Proton prefix before MO2 can use it.
3. Download the official matching SKSE64 archive from the [SKSE download
   page](https://skse.silverlock.org/) / its linked Nexus page. As of 2026-09-01,
   Steam runtime **1.7.104** needs **SKSE64 2.3.1**. Place the untouched archive
   at `ROMS_FINAL/PC/SKSE/skse64_2_03_01.7z` (or override
   `dg_skyrim_skse_staging_dir`).

Then run:

```sh
cd ansible
ansible-playbook install-skyrim-mo2.yml
```

The role refuses a different `SkyrimSE.exe` runtime and checks that the staged
archive contains `skse64_1_7_104.dll`. This is intentional: Bethesda updates
break the SKSE pairing, and an installer that silently applies an older loader
is worse than a clear stop.

## Playing and modding

Launch the generated helper from inside the box:

```sh
~/bin/skyrim-mo2
```

The same helper is available from the host app menu as **“Skyrim SE — Mod
Organizer 2 (on gaming)”** after running the playbook.

It runs MO2 through the game’s Steam Proton prefix. On the first MO2 launch,
select **Skyrim Special Edition** if prompted. In MO2’s executable selector,
add or select:

```text
<Skyrim Special Edition>/skse64_loader.exe
```

Name it `SKSE`, select it, and use **Run**. Start modded Skyrim through this
MO2 `SKSE` entry—not Steam’s vanilla Play button—so MO2’s virtual filesystem,
profile, and load order are active.

The recipe currently pins MO2 **2.5.0**. MO2 2.5.2 has an open upstream crash
at `ModOrganizer.exe+0x1b675` (including on native Windows); update the recipe
after that regression is resolved upstream.

MO2 itself is installed in `~/tools/mod-organizer-2/skyrim-se/`, outside the
Steam game directory; its own archives, downloads, mods, profiles, and
overwrite data therefore remain separate from Steam-verified files. SKSE only
places its loader/runtime files beside `SkyrimSE.exe` and its scripts under
`Data/`.

## Updates and repair

After any Bethesda update, rerun vanilla Skyrim once and compare its runtime
against [SKSE’s current download page](https://skse.silverlock.org/). Update
`dg_skyrim_runtime_version`, `dg_skyrim_skse_version`,
`dg_skyrim_skse_archive_name`, and `dg_skyrim_skse_runtime_dll` together before
rerunning the recipe. SKSE plugins may require updates independently.

The accompanying [research note](skyrim-mo2-research.md) records the source
verification and why this recipe does not use the currently stale stable
MO2-LINT SKSE table.
