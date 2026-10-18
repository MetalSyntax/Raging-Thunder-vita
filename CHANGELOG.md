# Changelog — Raging Thunder (PS Vita)

## v1.0.1 (2026-10-17)

Fixes from tester reports.

- Race buttons: Raging Thunder 1 keeps the engine's generic key masks, so
  Cross was nitro and Square/Triangle did nothing. Cross now accelerates,
  Square brakes and Triangle is nitro (L/R unchanged).
- Start now always pauses (the back key is held 6 frames instead of 1).
- Lighting/shadows no longer jump between meshes (vitaGL normal matrix was
  not refreshed after an unlit draw).
- Start + Select menu: no more input lag (redrawn only when it changes).
- Docs: in the game menus Triangle is OK and Cross does nothing.

## v1.0.0 (2026-10-10)

First public release. Menus and races playable on real hardware, no crashes.

- Loader synced from the hardware-tested Raging Thunder 2 port (same Polarbit
  Fuse engine, JNI table verified identical from the real `.so` files).
- vitaGL vendored (softfp), GLES 1.1, MSAA up to 4x, 960x544.
- Controls: physical buttons, touch, stick/motion steering, remappable menu.
- Audio through `SceAudioOut`, saves under `ux0:data/ragingthunder/`.
- Known: progress saving not yet tested.
