# Phosphor HUD — Weapon Artwork Part 1 Handoff

Last updated: 2026-11-08

## Purpose

This file is the continuation brief for future ChatGPT conversations. Read it before changing the weapon-artwork project so work can continue without relying on chat memory.

## Part 1 scope

- Target: a usable Part 1 milestone with at least 100 distinct weapon SVG files.
- Current branch: `feature/tactical-weapon-ammo-hud`.
- Repository: `prashanthsn/fo4-hud`.
- Fallout 4 runtime target: Next-Gen 1.10.984.
- User uses Mod Organizer 2 and beta PrismaUI NG.
- Keep `main` and the user's stable MO2 installation untouched.
- User wants the original Phosphor HUD layout preserved: ammo count above weapon art; art below it. Do not restyle unrelated HUD elements.
- Artwork target: solid pale/white filled, transparent-background side-profile silhouettes. Shapes should be recognisable and detailed, not generic outline icons.

## Current Part 1 inventory

The weapon assets folder currently contains **107 SVG files**. The initial 78 were already present in the branch. This Part 1 continuation added 29 profiles, including:

- 10mm pistol, .45 pistol, .44 revolver
- Pipe pistol, pipe rifle, pipe bolt-action
- Double-barrel shotgun, Combat Shotgun
- Assault Rifle, M4, M14, R91
- MP5, UMP45, P90, Uzi
- Plasma Gun, Laser Rifle, Institute Pistol, Plasma Caster
- Gauss Minigun, energy carbine, laser/plasma hybrid
- Lever-SMG, hand cannon, bolt-action rifle, automatic pistol, compact shotgun

New files were added in the folder:
`mod/Phosphor HUD/PrismaUI_F4/views/PhosphorHUD/assets/weapons/`

The HUD glyph registry is in:
`mod/Phosphor HUD/PrismaUI_F4/views/PhosphorHUD/hud.html`

Weapon name-to-art selection is in:
`src/Live.cpp` in `WeaponIcon()`.

## Important technical constraints

- User does not have local Visual Studio/C++ build tools; build via GitHub Actions.
- Do not ask user to run FO4Edit scripts. Their FO4Edit 4.1.5f crashed with runtime error 216 / hard crash.
- Do not ask user to install intermediate builds or test one-off batches.
- Only ask for in-game verification after a consolidated Part 1 package is ready.
- Do not change ammo logic or unrelated HUD behaviour unnecessarily.
- Keep all development on the feature branch.
- Existing weapon detection was previously working in a smaller test package; artwork quality and coverage are the focus.

## User-provided catalogue context

The user previously provided:
- `FO4_Weapon_Export.csv`
- `plugins.txt`
- `loadorder.txt`

Export columns: Plugin, FormID, EditorID, DisplayName, ModelPath, AmmoName, AmmoFormID, Keywords.
The raw export had 1,620 WEAP rows and 119 distinct plugins. MO2 plugins list had 627 active plugins and loadorder 643 entries.
The export includes NPC/turret/dummy records as well as player-usable weapons. Do not equate every QuickkeyGun record to a unique player weapon. Use winning override records and deduplicate names/forms before deciding missing artwork.

## Part 1 branch state

The consolidated Part 1 mapping update is in commit:
`ffd39fb7e65542c986f928865a19b53ea0e6fec1`

The later seven artwork additions were committed through:
`319eff9dd91d364dbacc3d795ad3c1640d48b990`

The branch head should be checked live before continuing:
https://github.com/prashanthsn/fo4-hud/tree/feature/tactical-weapon-ammo-hud

## Build and packaging workflow

GitHub Actions workflow:
`.github/workflows/build.yml`

Workflow name: `Build PhosphorHUD`.
It builds on Windows using xmake and CommonLibF4-NG, then uploads artifact `PhosphorHUD-build` containing:
- `build/release/PhosphorHUD.dll`
- `.build/xmake.log`

For Part 1 delivery:
1. Wait for the workflow on the final branch HEAD to complete successfully.
2. Download its `PhosphorHUD-build` artifact.
3. Verify the DLL exists and is non-empty.
4. Package the DLL together with the updated `hud.html` and the complete `assets/weapons/` folder, preserving relative paths.
5. Provide the user one MO2-installable archive and a separate handoff document.
6. Do not claim an in-game test has passed; the user will perform that once the consolidated package is supplied.

## Next work after Part 1

- Review the 107 SVGs visually for recognisability, consistency, and duplicates; count files alone does not certify art quality.
- Audit every weapon name mapping against the actual winning records in the supplied export.
- Correct any alias that sends a weapon to the wrong silhouette.
- Ensure all new glyph keys have matching SVG files and are referenced by `WeaponIcon()`.
- Check for duplicate mapping keys and overly broad substring matches.
- Build and package Part 1, then let the user play-test it.
- For Part 2, continue the catalogue audit and add artwork for the next group of genuinely distinct player-usable weapons.

## User communication

The user has explicitly asked for a tangible Part 1 milestone at 100+ unique weapon artworks, the DLL, updated HTML, and a document that enables work to continue in another conversation. Avoid indefinite progress updates. Report actual branch commits, build result, and artifact/package links only after verifying them.
