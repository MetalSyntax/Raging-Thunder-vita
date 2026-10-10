<p align="center">
  <img src="extras/livearea/icon0.png" width="128" alt="Raging Thunder icon" />
</p>

<h1 align="center">Raging Thunder — PS Vita Port</h1>

<p align="center">
  <a href="https://github.com/MetalSyntax/Raging-Thunder-vita/releases"><img alt="Latest release" src="https://img.shields.io/github/v/release/MetalSyntax/Raging-Thunder-vita?style=flat-square" /></a>
  <img alt="Platform" src="https://img.shields.io/badge/platform-PS%20Vita-blue?style=flat-square" />
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-green?style=flat-square" /></a>
</p>

<p align="center">
  Unofficial port of <b>Raging Thunder</b> (Polarbit, Android) to the PS Vita,
  running the original ARM game library through a native loader
  (<a href="https://github.com/TheOfficialFloW">TheFloW</a>'s so-loader + FalsoJNI) with rendering via vitaGL.
</p>

> **Disclaimer:** this project contains no game assets. You must provide your own
> legally obtained copy of the game (APK v1.2.1). See [Installation](#installation).

---

## About

Raging Thunder is an arcade racing game originally released for Android (Polarbit Fuse engine, GLES 1.1).
This port loads the unmodified `librthunderlite.so` (`armeabi`) and re-implements the
Android/Java layer it expects: lifecycle, EGL/GLES 1.1, touch/key/accelerometer input,
audio mixing, file access and LiveArea — so the game runs natively on the Vita.

The loader is shared with the [Raging Thunder 2 port](https://github.com/MetalSyntax/Raging-Thunder-2-vita)
(both games run the same engine — identical JNI table, verified from the real `.so` files).

- Gameplay: menus and races playable on real hardware, no crashes.
- Rendering: vitaGL (vendored, softfp), MSAA up to 4x, 960×544.
- Input: buttons, touch screen, left-stick tilt emulation and real motion controls.
- Audio: engine mixer streamed through `SceAudioOut`.
- Saves and settings stored under `ux0:data/ragingthunder/`.

## Requirements

On the Vita:

| Requirement | Notes |
|---|---|
| HENkaku / Enso | Any recent firmware with homebrew support |
| [kubridge.skprx](https://github.com/TheOfficialFloW/kubridge) | Required, loaded in `taiHEN` config (`*KERNEL`) |
| [libshacccg.suprx](https://github.com/TheOfficialFloW/ShaRKBR33D) | Required at `ur0:data/libshacccg.suprx` (via ShaRKBR33D) |
| Game data files | Copied from your own APK (see below, not distributed) |

## Installation

1. Install `ragingthunder.vpk` from the [latest release](https://github.com/MetalSyntax/Raging-Thunder-vita/releases) with VitaShell.
2. From your own `raging-thunder-free-1-2-1.apk` (open it as a ZIP), copy:
   | APK path | Vita destination |
   |---|---|
   | `lib/armeabi/librthunderlite.so` | `ux0:data/ragingthunder/main.so` |
   | `assets/data.vfs` | `ux0:data/ragingthunder/assets/data.vfs` |
3. Launch the game. The `logs/` and `saves/` folders are created automatically.

> `config.txt` is generated on first boot at `ux0:data/ragingthunder/config.txt`.
> Logs go to `ux0:data/ragingthunder/logs/ragingthunder_NNN.log`.

## Controls

Default layout (remappable, see below):

| Vita input | In-game action |
|---|---|
| ✕ Cross / R trigger | Accelerate (OK in menus) |
| ■ Square / L trigger | Brake / reverse |
| ▲ Triangle | Nitro / action |
| ● Circle / Start | Back / pause |
| D-Pad | Menus |
| Left stick | Steering (emulated tilt; D-Pad in menus) |
| Touch screen | Menus and HUD |
| **Start + Select** | **PS Vita controls menu** |

Steering follows the game's tilt mode: when a race uses tilt steering, the left stick
emulates the accelerometer; choose *Tilt (motion)* in the controls menu (or set
`steering 1` in `config.txt`) to use the Vita's real motion sensor instead.

### Remapping

Press **Start + Select** at any time (menus or race) to open the **PS Vita controls**
menu. The game is paused while it is open (clock frozen, audio muted) and the menu
is drawn over a dimmed copy of the last frame.

| In the menu | |
|---|---|
| D-Pad up/down | Choose a row |
| ✕ on an action | Replace its buttons (press the new button) |
| ■ on an action | Add another button |
| ▲ on an action | Clear it |
| D-Pad left/right | Change steering mode, sensitivity, invert |
| ● / Start | Save and close |

Bindings are saved to `ux0:data/ragingthunder/controls.txt`, which can also be edited
by hand (created with the defaults on first boot):

```ini
ACCELERATE = CROSS
BRAKE = SQUARE
NITRO = TRIANGLE
BACK = CIRCLE
L = L1          # shoulder keys: brake / accelerate in the race,
R = R1          # page switch in the menus
UP = UP
DOWN = DOWN
LEFT = LEFT
RIGHT = RIGHT
```

Buttons: `CROSS, CIRCLE, SQUARE, TRIANGLE, L1, R1, UP, DOWN, LEFT, RIGHT, SELECT, NONE`.
Start is always back/pause and is not bindable. `BUTTON = ACTION` lines are accepted too.

## Settings

`ux0:data/ragingthunder/config.txt` (created on first run):

| Key | Default | Description |
|---|---|---|
| `language` | `0` | 0 = system, 1–6 = fixed language |
| `steering` | `0` | 0 = left stick, 1 = Vita accelerometer |
| `steer_sensitivity` | `100` | 25–400 |
| `invert_steering` | `0` | 0/1 |
| `show_fps` | `0` | 0/1, FPS overlay |
| `msaa` | `2` | 0 = off, 1 = 2x, 2 = 4x |
| `engine_log` | `0` | 0/1, verbose engine log (for debugging) |
| `vfp_float` | `1` | 0/1, hardware float helpers |
| `xperia_pad` | `1` | 0/1, Xperia Play button layout |

## Building from source

Requirements: [vitasdk](https://vitasdk.org/), CMake, Git.

```sh
git clone https://github.com/MetalSyntax/Raging-Thunder-vita.git
cd Raging-Thunder-vita
mkdir build && cd build
cmake ..
make
```

This produces `eboot.bin` and `ragingthunder.vpk`. vitaGL is vendored under
`vendor/vitaGL` and builds automatically with the required `SOFTFP_ABI=1` flags.

> If your checkout path contains spaces, build through
> [`psvita-toolkit`](https://github.com/MetalSyntax/psvita-port-toolkit)
> (`psvita-toolkit build`), which stages the tree in a temp dir — `vita-pack-vpk`
> breaks on paths with spaces.

## Known issues

- Progress saving is implemented but **not yet tested** — please report whether
  career progress persists after restarting.
- Online features (news / multiplayer) attempt raw sockets without `sceNetInit` — they fail gracefully but have no connectivity.
- Quitting from the main menu takes a few seconds (the engine releases its resources in `OnDestroy`).

Found a bug? Please open an issue with your log file
(`ux0:data/ragingthunder/logs/ragingthunder_NNN.log`) attached.

## Credits

- Polarbit — original game.
- TheFloW — so-loader, kubridge, ShaRKBR33D.
- Andy Nguyen, Rinnegatamante, Volodymyr Atamanenko — loader boilerplate, FalsoJNI, vitaGL.
- Contributors to this port and to [Raging-Thunder-2-vita](https://github.com/MetalSyntax/Raging-Thunder-2-vita) — JNI table, input/audio/graphics glue, LiveArea.

## License

MIT — see [LICENSE](LICENSE). The original game and its assets remain property of their respective owners.
