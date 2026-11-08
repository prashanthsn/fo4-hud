# Weapon / Ammo HUD extension (design + integration contract)

This feature extends the existing Phosphor bottom-right `#kit` widget. It does not create a second counter or move the current ammo display.

## UI target
- Keep the existing `#kit` anchor (`right:56px; bottom:56px`) and existing large magazine / reserve typography.
- Put a small category glyph to the left of the existing weapon name.
- Put ammo display name in a subtle caption below magazine/reserve.
- Use inline SVG glyphs and the current Phosphor palette, not an opaque panel.
- In no-magazine cases, hide clip/reserve values as existing CSS does and keep the weapon category/name; show the matching ammo caption only when ammo data exists.
- Do not modify or override Condition Boy/figure, companion portrait, or other `#kit` children.

## Data contract addition
Add optional fields to `hudUpdate`:
- `weaponClass`: one of `pistol`, `rifle`, `shotgun`, `heavy`, `energy`, `melee`, `thrown`, `other`.
- `ammoName`: equipped ammo form's localized full name (e.g. Fusion Cell, 5.56 Round).
- `ammoKind`: one of `ballistic`, `energy`, `explosive`, `other`.

All are optional and additive, so older plugin builds continue working. On missing fields the UI should retain fallback behavior and never display a fabricated ammo label.

## Native source work
The current `ReadEquipped` already captures `TESObjectWEAP*`, `TESAmmo*`, clip and magazine capacity. `ReadSlow` emits weapon, clip, clipMax and reserve. Add a display-only class field based on the weapon's documented `WEAPON_TYPE` enum and an ammo name from the existing `TESAmmo*` full-name accessor. Do not add engine pointer reads to the PrismaUI/UI thread. The source tree's `Live.cpp` already centralizes these reads on the game task queue.
- Weapon categories need a verified enum mapping from the exact CommonLibF4-NG checkout used for this build; do not infer enum integers from the name or bit flags.
- Ammo classification must be conservative. Prefer ammo form type/keywords known in the selected CommonLib version; otherwise derive `ammoKind` as `other` and always display the actual ammo name.
- No FormID hardcoding: modded ammo should naturally show its form name.

## UI implementation tasks
1. Add a small glyph slot and caption to the existing ammo block in `hud.html`.
2. Extend `S`, `renderAmmo()`, and `hudUpdate()` for the three optional fields.
3. Classify glyph with a fallback outline icon if `weaponClass` is absent/unrecognized.
4. Ensure CSS stays anchored bottom-right and responsive in the 1920x1080 design stage.
5. Add browser tests for vanilla-style, energy, modded/custom, melee/no-ammo and old-message-with-no-new-fields cases.

## Runtime / release gate
The repo README currently says the personal NG port accepts Fallout 4 1.10.984, while some engine layouts are explicitly marked OG-verified only. No native DLL should be claimed tested for 1.10.984 without an xmake/MSVC build against the user's precise CommonLibF4-NG and PrismaUI API header, and an in-game test with the user's NG runtime. Keep Phosphor settings untouched:
`bLiveHud=1`, `bHideVanillaMeters=1`, `iLocationMarkerMeters=100`, `iKeepVanillaWidgetMask=256`.
