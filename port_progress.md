# Registro de Progreso — Raging Thunder (PS Vita)

## Fase 1: Configuración y Preparación (Completada — 2026-10-10)
- Repo desde soloader-boilerplate, `.gitignore` anti-DMCA (APK/ZIP, extract, jadx, .so).
- APK `raging-thunder-free-1-2-1.apk` (6.1 MB) extraído a `ragingthunder_extract/`.
- ABI: armeabi, x86 (elegida: armeabi). `.so`: `lib/armeabi/librthunderlite.so` (1756084 B).
- GLES: sin `glEsVersion` en el Manifest; confirmado 1.1 por análisis (Fase 3).
- Pendiente: primer commit (repo con 0 commits). Submódulo `lib/falso_jni` venía vacío
  (SSH bloqueado) → vendorizado por HTTPS en la misma ruta (10 archivos, incl.
  `converter.c/h` que faltaba en el build).

## Fase 2: Decompilación (Completada — 2026-10-10)
- jadx: SÍ estaba corrido (`decompiled/apk_jadx/` con sources + resources). El registro
  anterior decía "NO corrido" — corregido tras verificar el contenido.
- Ghidra (.so): NO corrido (daemon de Docker caído) — no hizo falta: el motor se
  identificó con `objdump -T` + volcado de la `JniTable` con pyelftools (ver Fase 3).

## Fase 3: Análisis del Motor Real (Completada — 2026-10-10)
Motor **Polarbit Fuse** (`com.polarbit.fuse.*`). Evidencia (no analogía):
- 12/12 exports `Java_*` + `JNI_OnLoad`; sin `RegisterNatives`; sin `pthread_create`.
- NEEDED = liblog, libdl, libc, libm. UND = 60 imports, todos cubiertos por la tabla.
- Ciclo de vida (MainTask.java + RenderThread): `FuseOnInit(act,task,sensor,utils,
  audio,egl)` → `OnEvent(3,0,w,h)` → `OnCreate(apkPath, filesDir+"/")` → loop
  `OnEvent(0,1,0,0,0)` (= 1 frame: FlushEvents + Run + `EglUpdateDisplay` = swap)
  → `OnEvent(0,0)` + `OnDestroy`. Audio: el motor mezcla (`PAudio*`), Java solo
  bombea `AudioCreate/AudioMix`. GLES por `dlopen("libGLESv1_CM.so")` (rama 1.1).
- **Motor hermano confirmado**: `Raging-Thunder-2-vita` usa el mismo motor —
  `JniTable` (.data, 0x528 B): las 178 strings apuntadas son idénticas en orden y
  contenido en ambos .so (nuestro offset 0x1a3fa0 vs 0x2a1ea4 del hermano).
  UND difiere solo en `bsearch`+`time` (hermano) vs `usleep` (nuestro); la tabla
  sincronizada cubre la unión.
- `jni-analyze`: middleware "Box2D" (sin verificar; irrelevante para el boot).
- `align-check`: limpio (sin ldrd/vld1).

## Fase 4: Bootstrap (casi completa — 2026-10-10, falta arranque en consola)
- **Decisión**: sync completo del loader probado en hardware del hermano
  (menus + carreras jugables, v1.1.1) en vez de redescubrir: `source/` (main, java
  54/54 JniTable 1:1, input, overlay, vita_menu, patch, dynlib, reimpl, utils),
  `vendor/vitaGL` (SOFTFP_ABI=1 — la prebuilt del SDK sin softfp = pantalla negra),
  CMakeLists adaptado (PSVRT0001, `ux0:data/ragingthunder/`).
- Mi primer `java.c` a mano tenía nombres/tipos inventados en ads/billing/facebook
  (la tabla real dice `createAdView`, `initiatePurchase`, `Show()->int`,
  `play()->int`...) — descartado por el del hermano, que es 1:1 con el dump.
- Fixes del hermano que heredamos: tabla nombres-VBO (`gl_buffers.c`), UV `float3`
  + sync de subidas en vitaGL vendorizado, `stat64` biónico, guarda
  `DeleteGlobalRef`, IME para InputDialog, `getenv`/`setenv` reales, `fseeko` por
  SceLibc, `vfprintf`/`glLightf`/`glLightx`/`gai_strerror` stubs.
- `patch.c` (tinte de autos) apunta a `CCarActor::Render` (solo RT2; RT1 usa
  `Car::`): con guarda de bytes, avisa y no parchea en RT1. Evaluar colores en
  consola antes de portar el parche.
- **Build OK** (`psvita-toolkit build`): `build/ragingthunder.vpk` (688 KB),
  `eboot.bin`, `ragingthunder.elf` (símbolos para dumps). Lecciones de build:
  `converter.c` agregado al CMake; `source/reimpl/egl.c` fuera del build (choca
  con el EGL de vitaGL); regla `Makefile` del `.gitignore` acotada a `/Makefile`
  (el `rsync --filter` del toolkit es first-match y se comía
  `vendor/vitaGL/Makefile`).
- **Deploy OK**: `ragingthunder.vpk` → `ux0:/downloads/`; `main.so` (1756084 B) +
  `assets/data.vfs` (2070375 B) → `ux0:/data/ragingthunder/` (tamaños verificados
  por FTP). `log-standard --fix-dirs` OK; `clean-junk` OK (3248 `._*`).
- **PRUEBA EN CONSOLA (2026-10-10, usuario)**: el juego **funciona y no crashea**
  (menús + carreras). Fase 4 completa.
- **ISSUE ABIERTO**: guardado de partida no probado — verificar si el progreso
  persiste tras reiniciar (`saves/`, `CSaveManager`). Si falla, triage con log
  `ragingthunder_NNN.log` + `so-crash-triage`.

## Fase 5 en adelante: Pendiente de hardware
Implementación sincronizada del hermano; verificar en RT1 uno por uno:
gráficos (pantalla negra si flags vitaGL mal), texturas DXT, audio SceAudioOut,
input Xperia Play + táctil + stick/acelerómetro, `saves/`, IME, red (sockets sin
sceNetInit), salida limpia, FPS/MSAA, menú START+SELECT, LiveArea/VPK final.
Ver PORTING_PLAN.md secciones 4-5/10 y el `port_progress.md` del hermano
(Pruebas 1-3, v1.1.0, v1.1.1) para los patrones ya resueltos.
