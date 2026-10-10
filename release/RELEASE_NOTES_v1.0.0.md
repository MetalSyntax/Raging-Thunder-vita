# Raging Thunder Vita — v1.0.0

First public release: the game (menus + races) runs on PS Vita without crashes.
Tested on real hardware (firmware with HENkaku/Enso, kubridge + libshacccg).

## What's included

- `ragingthunder.vpk` — install with VitaShell (Title ID `PSVRT0001`).
- `LEEME.txt` — quick install guide in Spanish.

## Installation

1. Make sure `kubridge.skprx` is loaded (`*KERNEL` in taiHEN config) and
   `ur0:data/libshacccg.suprx` exists (ShaRKBR33D).
2. Install the VPK.
3. From your own `raging-thunder-free-1-2-1.apk` copy:
   - `lib/armeabi/librthunderlite.so` → `ux0:data/ragingthunder/main.so`
   - `assets/data.vfs` → `ux0:data/ragingthunder/assets/data.vfs`
4. Launch the game. Saves, settings (`config.txt`) and logs are created
   automatically under `ux0:data/ragingthunder/`.

> The APK and its `.so`/assets are **not** distributed here — use your own copy.

## Highlights

- Native loader for the Polarbit Fuse engine (GLES 1.1 via vendored vitaGL with
  softfp, MSAA up to 4x), shared with the Raging Thunder 2 port (identical JNI
  table, verified from the real `.so` files).
- Controls: Cross/R accelerate, Square/L brake, Triangle nitro, Circle/Start back/pause,
  D-Pad menus, left stick steering (tilt emulation) or real motion sensor (`steering 1`),
  touch screen for menus/HUD, remappable PS Vita controls menu (Start + Select).
- Engine audio mixer streamed through `SceAudioOut`.
- Includes the hardware-proven fixes from the sibling port: VBO name handling,
  corrected environment reflections, bionic `stat64` layout, clean exit from
  the game menu.

## Known limitations

- Progress saving is implemented but **not yet tested** — please report whether
  career progress persists after restarting.
- News/multiplayer features have no connectivity (raw sockets, fail gracefully).
- Quitting from the main menu takes a few seconds (the engine releases its
  resources in `OnDestroy`).

## Reporting bugs

Attach `ux0:data/ragingthunder/logs/ragingthunder_NNN.log` (set `engine_log 1`
in `config.txt` for a verbose log) to your issue at
<https://github.com/MetalSyntax/Raging-Thunder-vita/issues>.
