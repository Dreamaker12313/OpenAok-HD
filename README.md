**[简体中文](README.zh-CN.md)** | [English](README.md)

# OpenAokHD

**A native Android port of *Age of Empires II: HD Edition*, with touch controls — based on OpenAoK + Thor AoK.**

OpenAokHD runs from the player's own **Age of Empires II: HD Edition (2013)** data on Android arm64.
It supports 31 civilizations, the original, The Conquerors and the HD expansions, and adds a touch command deck for dual-screen handhelds such as the AYN Thor.

> This repository contains **no game data**. You need your own copy of *Age of Empires II: HD Edition*.
> Not affiliated with, endorsed by or sponsored by Microsoft / Forgotten Empires.

---

## Features

### Game content

- **31 civilizations** (including the HD DLC civilizations; Gaia is counted separately)
- Campaign entries for **The Age of Kings / The Conquerors / African Kingdoms / Rise of the Rajas / The Forgotten**
- HD DLC campaigns converted from `.cpx2` / `.aoe2campaign` into the engine's `.cpx`
- The Conquerors and HD random maps, plus real-world maps
- The 18 loose official HD random-map scripts (`Green Arabia`, the `es_*` set) are loaded from the data pack
- The Conquerors / HD expansion AI files (`gamedata_x1`, `gamedata_x2`) are included in the data pack
- Chinese UI, unit, technology and civilization names
- Chinese line breaking by character, white text with an eight-direction black outline

### HD interface

- Decodes the HD **32-bit SLP** files (`main_32.slp`, `xcam*_32.slp`, `scr*_32.slp`, …)
- HD backgrounds for the main menu, campaign book, campaign maps and custom campaign pages
- HD backgrounds are drawn as a separate layer while the original button/sign frames are kept, so replacing art does not break buttons
- Campaign maps use each DLC's own `*_campaign_render.jpg` / `maps/*.jpg`
- *The Forgotten* uses its official campaign-map art: 7 campaign pages with map backgrounds, 3D markers, hover descriptions and real `.cpx` mission text

### HD in-game interface

- A native **1280×720** interface layout for 16:9 frames (1688×768 on the Thor), instead of stretching the 800×600 layout
- DE-style split bottom bar: building/command panel + unit panel on the left, a separate minimap block on the right
- The minimap borrows each civilisation's own bottom-panel material and has an independent wooden frame
- Centred age label, re-aligned resource bar and top-right buttons
- Pause menu keeps the game visible behind it (no full-screen black panel)

### Android / touch

- arm64 Android 29+
- Dual screen: map on the top screen, touch command deck on the bottom screen
- Single-screen devices fall back to the original layout
- Data import: pick the `OpenAoK-data` folder; the app verifies the manifest and copies it into private storage
- App name **OpenAokHD**, king-portrait rounded icon

---

## Data pack

The repository does **not** include game data. Generate it on your PC:

```sh
python3 -m pip install pillow numpy
python3 android/package.py --hd "/path/to/Age2HD"
```

Output:

```text
android/build/OpenAoK-data/
```

Then:

1. Copy `OpenAoK-data` to the phone (SD card or internal storage).
2. Start OpenAokHD.
3. Choose the `OpenAoK-data` folder.
4. Wait for verification/copying, then play.

The pack is about **1 GB** and contains:

- HD graphics, sounds, terrain and language files
- DLC civilizations and unit data
- 15 HD campaigns (4 African, 4 Rajas, 7 Forgotten)
- HD interface backgrounds and campaign illustrations

---

## Building

### Android

Requirements:

- Android SDK + NDK (arm64)
- CMake / Ninja
- The included `android/` Gradle project

```sh
cd android
./gradlew assembleDebug
```

The APK is written to `android/app/build/outputs/apk/debug/`.

> Sign release builds with your own key. Never commit `local.properties`, `*.jks` or `build/` output.

### Desktop

```sh
./build.sh
cd build && ./OpenAoK
```

The desktop build is mainly for debugging engine logic; the Android touch deck is enabled only in Android builds.

---

## Source layout

```text
src/
  ai/            computer players
  core/          rules, maps, input
  ecs/           units, buildings, combat, triggers
  hal/           SDL, window, SLP, audio, networking
  rendering/     map, units, UI, text
  screens/       main menu, campaigns, setup, dialogs
  scenario/      scenario and campaign I/O
  save/          saved games
tools/data/      HD data conversion, SLP/DRS/campaign tools
android/         Android project and data packaging
third_party/     SDL2, genieutils, FreeType, etc.
```

HD conversion tools:

- `tools/data/convert_hd.py` — HD data layout conversion
- `tools/data/extract_interface_data.py` — interface/campaign resource index
- `tools/data/convert_dlc_campaigns.py` — HD DLC campaign conversion
- `tools/data/slp.py` — 8-bit SLP reader/writer
- `tools/data/encode_rect_slp.py` — RGB image to 8-bit SLP
- `tools/data/slp32.cpp` — 32-bit HD SLP to PPM

---

## Known limitations

- **Base / Conquerors campaign intros**: the `.MM` slideshow is still handled through the mission page + manual replay; full automatic `.MM` playback is not finished.
- **Forgotten / DLC voice-over**: Chinese narration exists for the base game and The Conquerors; the HD expansions do not have a complete Chinese dub.
- **Official random-map scripts**: the 18 loose `.rms` files are listed and loaded, but not every HD script keyword is guaranteed to be supported by the parser.
- **HD unit art** is still the 2013 HD 8-bit SLP; the Definitive Edition 32-bit unit assets are not decoded.
- The Forgotten campaign pages use official HD screenshots cropped into the data pack because the 2013 HD build has no separate static module art.
- The data pack is about 1 GB, so the first import is slow.

---

## License and credits

- Engine based on [OpenAoK](https://github.com/Zaydme/openaok) (C++20 / SDL2)
- Thor dual-screen/touch work based on **Thor AoK**
- 32-bit SLP decoding uses [genieutils](https://github.com/Tapsa/genieutils)
- SLP/DRS format references [openage](https://github.com/SFTtech/openage)
- *Age of Empires II* is copyright Microsoft / Forgotten Empires

See `LICENSE` for the code license and `THIRD_PARTY_NOTICES.md` for third-party components.
