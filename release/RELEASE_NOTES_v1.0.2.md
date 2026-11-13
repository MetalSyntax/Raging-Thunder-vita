# Raging Thunder Vita — v1.0.2

Bug-fix release based on tester reports.

## Fixes

- **Background flicker**: during races and in the garage the engine only clears
  the depth buffer, so areas not covered by the sky showed old frames and
  flickered. The port now clears the color buffer every frame.

## Install / update

Install `ragingthunder.vpk` over v1.0.1 with VitaShell. Game files
(`ux0:data/ragingthunder/main.so`, `assets/data.vfs`) and saves are kept.
First install: see `LEEME.txt` or the README.
