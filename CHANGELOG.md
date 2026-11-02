# Changelog

## 0.1.1 — 2026-11-02

### New
- **Settings during play:** Ctrl+L (or Select+L3 on a controller) reopens the
  launcher. Shader, window scale, volume and controls apply when you press
  Resume, without restarting the game.
- **Audio interpolation:** press **I** to cycle Gaussian (the console's own,
  default), Cubic (crisper highs) and Sinc. It changes only what you hear;
  the game, its echo and save states are unaffected. The choice is kept.
- New upscaler shaders: **ScaleFX**, **NNEDI3 (neural)** and **NNEDI3
  (neural, high)**.
- A fresh install starts with the **CRT Lottes** shader. An install that
  already has a shader chosen keeps it.
- **Updates never cost you a save.** From the next update on, the updater
  copies your save states to `save-backups\<date>_before_<version>\` before
  installing, leaves save files out of the copy and puts the backup back if
  they changed anyway. Save states from earlier versions keep working, and
  every release is now checked for that before it is published.

### Fixed
- HDMA raster effects (per-line color, layer and scroll changes) started one
  line late; they now start on the line the game asks for.
- Choosing **None** as the shader during play no longer leaves a black
  screen.
- Multi-pass shaders that read the original game frame from a later pass
  (ScaleFX) no longer draw black.

## 0.1.0 — 2026-11-01

First release: a setup pack that recompiles the game from your own ROM.

- Boots through the Electronic Arts and Visual Concepts logos and the title
  screen into the main menu, the campaign briefing, the map screen and the
  first mission.
- Scanline, CRT and upscaler shader presets; a fresh install starts with
  **CRT Geom (curved)**, and whatever you pick in the launcher is kept.
- **Check for updates on start** (next to *Skip launcher on boot*, on by
  default): installs newer releases over the install folder, keeping save
  states, settings and your ROM selection.
- Renderer fixes shared with the Super Mario Kart and Donkey Kong Country
  ports (per-line sprite tables, HDMA set up in vblank, vblank writes applied
  on time).
- Console-free Windows build; diagnostics go to `DesertStrikeSNESRecomp.log`.
