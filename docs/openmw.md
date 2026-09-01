# OpenMW (Morrowind)

[OpenMW](https://openmw.org/) is the native, open-source reimplementation of
the Morrowind engine. It is installed from Arch's `openmw` package and managed
by `install_openmw` / `install-openmw.yml` (`site.yml --tags openmw`).

OpenMW is an engine only: it does **not** include Morrowind's copyrighted game
data. Install an owned copy of *The Elder Scrolls III: Morrowind GOTY Edition*
through Steam **inside the `gaming` distrobox** first, then run:

```sh
cd ansible
ansible-playbook install-openmw.yml
```

The default Steam data path is
`~/.local/share/Steam/steamapps/common/Morrowind/Data Files/`. For a GOG or
manual install elsewhere, set `dg_openmw_game_dir` in
`ansible/host_vars/localhost.yml` to the directory that contains `Data Files/`.

The playbook verifies `Data Files/Morrowind.esm`, installs the native engine,
and renders a host **OpenMW** launcher. On its first launch, point the setup
wizard at the `Data Files` directory. Enable `Tribunal.esm` and `Bloodmoon.esm`
when those expansions are present; keep Bloodmoon after Tribunal in the content
list.

OpenMW supports normal content mods, but mods that depend on MWSE, MGE XE, or
MCP do not work because those modify the original Morrowind executable rather
than OpenMW.
