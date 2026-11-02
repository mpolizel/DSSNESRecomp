# Desert Strike Recomp

A native recompilation of **Desert Strike: Return to the Gulf** (USA), built on
[snesrecomp](https://github.com/RetroPortingToolKit/snesrecomp).

Run Desert Strike natively on Windows.

> **You must legally own a copy of the game.** No ROM data is distributed with
> this project, in the repository or in any release. The recompiled C is
> generated locally from your own copy and is never committed.

## ☕ Keep the ports coming

<p align="center">
  <a href="https://buymeacoffee.com/mpolizel"><img src="https://img.shields.io/badge/Buy%20me%20a%20coffee-support%20more%20ports-FFDD00?style=for-the-badge&logo=buymeacoffee&logoColor=black" alt="Buy me a coffee"></a>
</p>

**This project is free and always will be.** Every port in this series,
Desert Strike included, is built with many hours of AI-assisted reverse engineering,
and that AI time is paid. If you enjoy playing it, a coffee at
**[buymeacoffee.com/mpolizel](https://buymeacoffee.com/mpolizel)** keeps the AI running so **more SNES
games can be ported to native Recomp**. Any amount helps, and thank you!

## Download (ready-to-run Windows build)

Get the latest `desertstrike-<version>-windows-x64.zip` from the
[Releases page](https://github.com/mpolizel/DSSNESRecomp/releases/latest).

The zip is a **setup pack**: it contains the launcher, the recompiler and this
source tree (with the framework patches already applied), but no game data.
On first run:

1. Unzip anywhere and start `DesertStrikeSNESRecomp.exe`.
2. Let it download the portable build tools (one time per machine), or point
   it at a `cmake-clang-v1` zip you already have.
3. Pick your own `Desert Strike - Return to the Gulf (USA).sfc` (CRC32
   `4ce26787`).
4. Press **Generate & rebuild**. The playable game is built into `build/` and
   started; run it from there afterwards.

In the launcher, **Settings → Shader** lists the scanline, CRT and upscaler
presets; choosing one switches the renderer to OpenGL automatically. A fresh
install starts with **CRT Geom (curved)**; pick **None** for the plain picture.
Your choice is saved in `config.ini` and kept.

### Updates

With **Check for updates on start** ticked (the default; on the launcher's main
screen, next to **Skip launcher on boot**), the game asks GitHub for the latest
release when it starts and offers to install a newer one. Accepting closes the
game, downloads the new setup pack, copies it over the install folder and
reopens the setup window: press **Generate & rebuild**. Nothing is deleted;
`build\` (the rebuilt game, save states, `config.ini`, `keybinds.ini`) and
`rom.cfg` are kept. Save states made by an older version may not load in a
newer one. Untick the box to turn the check off (saved in `update.ini`).

The game has no battery save: campaign progress is kept by its passwords.

See [CHANGELOG.md](CHANGELOG.md) for what changed in each release.

## Status

Boots through the Electronic Arts and Visual Concepts logos and the title
screen into the main menu, the campaign briefing, the map screen and the first
mission (the Apache takes off from the frigate). Most code still runs through
the snesrecomp interpreter; full campaigns are not yet verified. See
[Porting from here](#porting-from-here).

### Framework patches

`patches/` holds changes to the `snesrecomp` and `recomp-ui` submodules.
Reapply them after updating the submodules:

```sh
git -C snesrecomp apply ../patches/snesrecomp-shader-forces-opengl.patch
git -C snesrecomp apply ../patches/snesrecomp-raster-oam-and-hdma.patch
git -C recomp-ui apply ../patches/recomp-ui-host-toggle.patch
```

`snesrecomp-raster-oam-and-hdma.patch` is the renderer fix shared with the
Super Mario Kart and Donkey Kong Country ports. The renderer draws after the
CPU frame and replays the frame's display writes line by line. It keeps each
sprite table (OAM) with the line it takes effect on, initializes HDMA channels
enabled in vblank before line 1, and files a write made early in a line with
the line before it, but never across the start of vblank, whose writes belong
to the next frame.

`recomp-ui-host-toggle.patch` lets a title add one checkbox of its own beside
"Skip launcher on boot"; this project uses it for the update check.

## ROM identity

| | |
|---|---|
| File | `Desert Strike - Return to the Gulf (USA).sfc` |
| Publisher | Electronic Arts |
| Developer | Visual Concepts |
| Year | 1992 |
| Mapping | lorom |
| Region | USA (North America) |
| Coprocessor | none |
| CRC32 | `4ce26787` |
| SHA-256 | `606abf536440173ae36466db360c7db6b474beb7a105c8a62bc74a54cbe1c38b` |

`tools/regen.sh` refuses to run against anything else, so a mismatched
revision fails immediately instead of producing subtly wrong output.

## Build

```sh
git submodule update --init --recursive
bash tools/regen.sh --rom "/path/to/Desert Strike - Return to the Gulf (USA).sfc"
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
```

The ROM does not have to live in the repository — keeping it on your own
drive is the better habit, and `SNESRECOMP_ROM` sets the path once for a
shell. Without either, `tools/regen.sh` finds the ROM at the repo root, then
the path the setup wizard recorded in `rom.cfg`; `.gitignore` blocks both from
ever being committed.

`tools/regen.sh` verifies the ROM, generates `src/gen/*.c`, and re-syncs
`recomp/funcs.h`. Re-run it whenever you change anything under `recomp/`.

The Windows build is a window app: diagnostics go to
`DesertStrikeSNESRecomp.log` beside the executable. `-DDS_CONSOLE=ON` brings
the console back for debugging.

## Run

```sh
./build/DesertStrikeSNESRecomp                         # launcher picks the ROM
./build/DesertStrikeSNESRecomp "/path/to/Desert Strike - Return to the Gulf (USA).sfc"
```

With no ROM on the command line the build opens the recomp-ui launcher: a ROM
picker with a verification badge, plus display / audio / input settings. Your
choice is cached in `rom.cfg` beside the executable, so the next launch opens
on it and "Skip launcher on boot" makes it immediate.

Either way the ROM is checked against the digests above before anything boots
— a dump that could not have produced this build is refused at the door rather
than mis-executing ten frames in.

## Layout

| Path | What lives there |
|---|---|
| `recomp/` | Analysis input: `bank*.cfg`, `symbols.toml`, generated `funcs.h` |
| `rom_identity.txt` | ROM digests — read by the build, `tools/regen.sh` and CI |
| `src/` | Host code you own: `main.c`, `game_rtl.c`, `ds_update.c` |
| `src/gen/` | Generated C. Never committed — regenerate locally |
| `assets/shaders/` | Scanline, CRT and upscaler presets for the launcher |
| `patches/` | Changes to the framework submodules (see above) |
| `snesrecomp/` | Framework submodule (owns `lib/recomp-net`, `lib/retcomm-rbengine`) |
| `tools/` | `regen.sh` — the ROM → C pipeline; `import_libretro_shaders.py` |
| `scripts/` | `package_release.sh` — player-facing zip |
| `framework_pins.txt` | Exact framework commits this project was scaffolded against |

## Porting from here

The scaffold stops where the game-specific work starts. In rough order:

1. **Make it boot.** `src/game_rtl.c` holds the frame driver. It starts on
   the framework's beam-aligned driver (`snesrecomp/runner/src/beam_frame_driver.h`):
   one frame per PPU field, interrupts taken where the beam latches them,
   the field rasterized from the raster journal.
2. **Name things.** Add entries to `recomp/symbols.toml` as you identify
   routines, then re-run `tools/regen.sh`. Set `emit = true` to promote one
   into ahead-of-time analysis; leave it false to keep it interpreted.
   See `recomp/README.md`.
3. **Resolve dispatch misses.** After every run, deal with unresolved
   indirect targets before anything else — they are the reason a port
   diverges, and they are cheap to fix early.
4. **Never synthesise a result** to get past uncovered code, and never edit
   `src/gen/` by hand. Fix the config or the framework and regenerate.

## Multiplayer

Single player.

## License

This project's own source is under the MIT license in `LICENSE`. The
framework carries its own terms (PolyForm Noncommercial 1.0.0) — see
`snesrecomp/LICENSE` and `snesrecomp/THIRD_PARTY_ATTRIBUTION.md`; the changes
in `patches/` apply to that framework and fall under its terms. The shaders
under `assets/shaders/_libretro/` come from libretro/glsl-shaders and keep each
author's license in their file headers. None of these cover the game data,
which is not distributed here.

---

<p align="center">
  Enjoying Desert Strike Recomp? <a href="https://buymeacoffee.com/mpolizel"><b>☕ Buy me a coffee</b></a> to help
  bring the next SNES game to native Recomp.
</p>
