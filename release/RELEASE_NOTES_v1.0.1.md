# Raging Thunder Vita — v1.0.1

Bug-fix release based on tester reports.

## Fixes

- **Race controls**: Cross accelerates, Square brakes, Triangle fires the nitro,
  R/L accelerate/brake. (Before, Cross was nitro and Square/Triangle did nothing:
  Raging Thunder 1 never remaps the engine's generic key masks, so the port now
  does it at boot.)
- **Start pauses reliably**: the back key is now held for 6 frames instead of 1,
  which the game sometimes missed.
- **Lighting / shadows**: lit objects (track, cars) no longer flash to another
  mesh's lighting. Fixed in vendored vitaGL: an unlit draw consumed the
  matrix-changed flag without recomputing the normal matrix.
- **Start + Select menu**: no input lag; the overlay is redrawn only when something changes.

## Controls (default)

| Vita | Race | Menus |
|---|---|---|
| ✕ Cross / R | Accelerate | — (does nothing) |
| ■ Square / L | Brake / reverse | L/R switch page |
| ▲ Triangle | Nitro | **OK / confirm** |
| ● Circle / Start | Pause | Back |
| D-Pad / left stick | Steer | Navigate |
| Start + Select | PS Vita controls menu | |

> If you already have a `controls.txt` from v1.0.0, it keeps your bindings;
> the new race actions apply to whatever buttons are bound to ACCELERATE / BRAKE / NITRO.

## Install / update

Install `ragingthunder.vpk` over v1.0.0 with VitaShell. Game files
(`ux0:data/ragingthunder/main.so`, `assets/data.vfs`) and saves are kept.
First install: see `LEEME.txt` or the README.
