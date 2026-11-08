# Phosphor HUD 0.9.24 — OG source (NG port patch applied)

Source for the Phosphor HUD F4SE plugin (Fallout 4 **1.10.163** OG; this copy also accepts **1.10.984** NG) and its PrismaUI HUD page. The playable mod is
the main download; this archive is optional and only needed if you want to read or build the code.

## What's in here

| Path | What |
|---|---|
| `src/` | The F4SE plugin (C++23). `Plugin.cpp` lifecycle and message loop, `Live.cpp` game/HUD reads, `Mods.cpp` optional-mod support, `Pipboy.cpp`, `Mines.cpp`, `EngineQuests.cpp` quest markers and map data read from the engine, `Core.cpp` / `ModData.cpp` engine-independent logic. `#if defined(PHOSPHOR_AE)` blocks belong to the separate AE experimental build and are inactive here. |
| `mod/Phosphor HUD/` | HUD page (`PrismaUI_F4/views/PhosphorHUD/hud.html`), MCM page, plugin INI and licenses. Portraits and figures are separate add-ons and are not included. |
| `docs/` | Architecture, the message contract between plugin and page, data sources, settings, build plan. |
| `schema/`, `examples/` | JSON Schema for one HUD message and example messages (also the in-game replay file). |
| `tests/` | Native core tests (`core_tests.cpp`) and browser tests for the HUD page (Python + Playwright). |
| `tools/` | Build, package, test and replay scripts. |

## Building

Requirements: Windows, MSVC 2022 (x64), [xmake](https://xmake.io) 3.x, a CommonLibF4-NG checkout, and the PrismaUI F4
2.1.1 plugin API header.

1. Put `PrismaUI_F4_API.h` from the PrismaUI F4 2.1.1 download (mod root, not the `F4SE/plugins` copy) into
   `third_party/`. It is PrismaUI's file and is not redistributed here.
2. Point `COMMONLIBF4_PATH` at your CommonLibF4-NG checkout.
3. Build: `xmake f -m release -y` then `xmake build PhosphorHUD`. The DLL lands in `build/release/`.
   Core tests: `xmake build phosphor-tests` then run `build/tests/phosphor-tests.exe`.
   `tools/build.ps1 -CommonLib <path>` wraps the same steps.
4. HUD page checks: `pip install playwright jsonschema`, `python -m playwright install chromium`,
   then `python tools/replay.py` (should print "all good") and `python tests/test_hud.py`.

## Tactical weapon/ammo HUD extension

The `feature/tactical-weapon-ammo-hud` branch places a large loaded/reserve ammo counter above a prominent weapon silhouette and full equipped weapon name. The native message fields are `weaponClass` (weapon-family label), `weaponIcon` (named silhouette profile with family fallback), and `ammoName` (equipped ammo form display name). The named profiles cover FAMAS, Origin 12, Saiga, Catalyst AAS, SVD/Dragunov, PP-19 Bizon, M1911, Desert Eagle, SIG P220/P226/P320/P322/P365, AER15, Service Rifle, StG-44, Stechkin APS, Archimedes-II and Minigun. Additional active-modlist aliases (including TT-33, SPAS-12, SV-98, G36, AKM, M249, Steyr AUG, FN FAL, MAC-11, Beretta M9, Glock P80 and PL-15) are routed to the closest available named profile; all remaining weapons use their detected family silhouette. Matching uses the base weapon form name so player-renamed weapons retain their correct icon. The user's supplied export contained 1,620 WEAP rows; resolving duplicate FormIDs against the supplied load order leaves 520 winning QuickkeyGun records across 325 display names. This includes NPC/turret records as well as player/inventory candidates, so it is not a count of 325 unique player weapons. The HUD uses inline vector silhouettes and does not need external image files. The native DLL must be rebuilt for the target runtime before testing in game.

## License

Phosphor HUD's own code is released under the MIT License (`LICENSE`). Third-party components keep their own
licenses: see `third_party/licenses/` and `mod/Phosphor HUD/licenses/`. The PrismaUI header is not included.

## NG port notes (unofficial, personal-use patch)

- `Plugin.cpp`: version data whitelists 1.10.163 and 1.10.984; `Query`/`Load` accept both; the log line says OG or NG.
- `HitEventSource()` uses `REL::RelocationID(1411899, 2201886)` (OG, NG) so it is right on both runtimes and with old or new CommonLibF4-NG checkouts.
- Everything else is unchanged. PrismaUI `SuppressHUDWidget` is documented as OG/AE only, so on NG the vanilla HUD may stay visible (the log shows `refused`).
- Raw engine layouts in `EngineLayout.h` were only verified on OG. All reads are `Readable()`-guarded; check `PhosphorHUD.log` on NG.
