# Changelog

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
