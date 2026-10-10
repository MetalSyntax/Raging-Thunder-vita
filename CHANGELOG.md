# Changelog — Raging Thunder (PS Vita)

## v1.0.0 (2026-10-10)

First public release. Menus and races playable on real hardware, no crashes.

- Loader synced from the hardware-tested Raging Thunder 2 port (same Polarbit
  Fuse engine, JNI table verified identical from the real `.so` files).
- vitaGL vendored (softfp), GLES 1.1, MSAA up to 4x, 960x544.
- Controls: physical buttons, touch, stick/motion steering, remappable menu.
- Audio through `SceAudioOut`, saves under `ux0:data/ragingthunder/`.
- Known: progress saving not yet tested.
