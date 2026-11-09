# TristanPbty's Armor Hud — A Native Mod for Horizon

A faithful native reimplementation of the minimal armor widget known from **Uku's Armor HUD** (itself a fork of BerdinskiyBear's mod), for [Horizon](https://github.com/horizon-rust/horizon), the Minecraft Java Edition client written in Rust.

No Java, no JVM, no Fabric/Forge/Quilt JAR: this is a **source mixin bundle** (`horizon-mixins`) that the Horizon launcher compiles into the game, delivered as a `.hnmod` file.

The widget displays equipped armor as item slots next to the HUD, featuring vanilla-style slot backgrounds, **enchantment glint**, per-item durability, and an animated low-durability warning — all positioned using anchors, sides, offsets, and scaling, and configured through an in-game screen with a live preview.

The mod **appears in the mod manager** (Options > Mods) with its name, version, description, configurable menu keybind, and a reset button.

By **TristanPbty**, built on **Tristan's Lib** (the shared `tristanpbty_lib` crate in this repository). **Both projects are version 1.0.0**, consistently across the mod, library, and manifest.

## Included Files

| File | Contents |
|---|---|
| `tristanpbty_armor_hud-1.0.0.hnmod` | The mod, to be placed in the game's `horizon-native-mods/` directory |
| `TristanPbty-Horizon-v1.0.0-source.zip` | Complete source code (Tristan's Lib + the mod + docs/ + branding/) |
| `tristanpbty_armor_hud.png` | Mod logo: the turtle helmet in its slot, with its durability bar |
| `tristanpbty_lib.png` | Tristan's Lib logo: the little character |

## Installation

1. Place the `.hnmod` file in the game's `horizon-native-mods/` directory (or submit it to the horizon-mods registry and install it from the launcher's Mods page).
2. The launcher compiles it using the pinned Nightly toolchain with its trust prompt (native mods run with your permissions; this is Horizon's security model, not a decision made by this mod).
3. In-game: press **H** (reconfigurable in Options > Mods or in the configuration screen) to open the configuration screen. Everything can also be edited manually in `config/tristanpbty_armor_hud.json`.
4. Options > Mods lists **TristanPbty's Armor Hud 1.0.0**. Disabling it there puts the mod on hold until the next launcher build.

## Emblem and Logos

- **In-game:** The configuration screen (H key) now features a header displaying the **vanilla turtle helmet** (`minecraft:turtle_helmet`, the actual in-game item, rendered by the game) next to the mod's name. This is the mod's emblem, visible whenever you configure it.
- **PNG logos** (the turtle helmet for the mod and the little character for the library) are ready for the **horizon-mods registry**: a square PNG between 64 and 1024 pixels per side, with a maximum size of 256 KiB. They are uploaded when submitting the project (`playhorizon.dev/mods/submit`, or through a pull request — the registry stores them in `icons/<id>.png`), and this is the logo displayed in the launcher's mod browser.
- **Why aren't they included in the `.hnmod`?** The format does not support this, without any workaround: `horizon-native.json` does not accept an icon field (unknown fields are rejected), and neither the in-game Options > Mods manager nor the launcher's local mod list displays icons for native mods. The icon belongs in the registry, which is why the emblem is rendered directly in the configuration screen.

## Implemented Features

- Anchors: `TOP_CENTER / TOP / CENTER / BOTTOM / HOTBAR`, sides: `LEFT/RIGHT`, X/Y offsets, `HORIZONTAL/VERTICAL` orientation, scaling, and reversed ordering.
- Slot styles sliced from vanilla hotbar sprites (`HOTBAR`, `ROUNDED_CORNERS`, `ROUNDED`, `NONE`).
- **Enchantment glint:** Each stack's glint state (Horizon’s own `has_foil` entries) is carried through the snapshot, and the reconstructed item glows just like the original.
- `widgetShown`: `ALWAYS / IF_ANY_PRESENT / NOT_EMPTY`; empty slot icons (vanilla `gui/sprites/container/slot/…`).
- Durability: the item's durability bar, or a number/percentage next to the slot, colored like the vanilla bar; off-hand and main-hand durability displayed next to the vanilla hotbar.
- Warnings based on remaining durability points or percentage, with time-based bouncing animation (deterministic, unlike the reference mod's random jitter).
- Off-hand spacing rules (`ALWAYS_IGNORE / ADHERE / ALWAYS_LEAVE_SPACE`), including the attack indicator case, and the `right_handed` mirror.
- `pushStatusEffectIcons`: the widget stacks below the effect rows using the HUD layer's corner reservation.
- A configuration screen built with the library's widgets, five tabs, a live preview, and a header emblem, using Horizon's mod screen API.
- **Mod manager entry:** The `mods` hook registers the entry (name, version, description, keybind), while the `settings`/`settings_click` pair adds the reset button.

## Not Implemented Yet, and Why (No Misleading Claims)

- **`pushBossbars` / `pushSubtitles`** are accepted, stored, and displayed in the configuration, but version 1.0.0 does not yet move these layers. Horizon's bossbar/subtitle layouts live in `horizon-client-render` without an exposed offset, and a mixin there cannot access this mod's state. An upstream hook will be required; the mod records the offset it *would apply*.
- **The warning icon** is a drawn exclamation mark, not the reference mod's `warn.png` texture (the mod does not ship any assets).
- **Glint** is implemented through a substitute enchantment (`minecraft:unbreaking`): vanilla glint only checks whether the list is non-empty, and the HUD does not display enchantment names (an OptiFine CIT edge case is documented in `docs/COMPATIBILITY.md`).
- The configuration screen cannot be opened on top of the Options screen (a mod screen can only be opened while in a world — a Horizon rule). This is why the Options > Mods page offers a reset button rather than an "open settings" button.
- **No icon in the `.hnmod` or local mod lists:** the format and Horizon interfaces at the pinned revision do not support this (see "Emblem and Logos"); the logo lives in the registry.
- The mod **does not modify the vanilla armor bar** (neither does the reference mod).

## Tests

`cargo test --workspace`: **79 tests** (64 in Tristan's Lib, 15 in the mod — the emblem and header have their own tests).

Beyond that, version 1.0.0 was **actually staged** against the pinned Horizon sources using the official horizon-mixins CLI: the plan validates (11 injections), the staged game compiles without warnings, and the game's own test suite passes (1151/1151) with the mod injected.

Details in `docs/TESTING.md`.

## Licenses and Acknowledgements

- This project: **MIT**, by **TristanPbty**. Both logos are original works by the author (the turtle helmet is original pixel art in the vanilla style; no Mojang assets were copied).
- `horizon-native-mod-template` (MIT) and levzzz5154's Veinminer packager: the packaging/staging approach, adapted.
- `uku3lig/armor-hud` (MIT) and BerdinskiyBear's original mod: behavior, geometry, and configuration terminology, reimplemented from scratch in Rust.
- `uku3lig/ukulib` (MPL-2.0): inspiration for the shared-library + mod structure. No code was copied (different language and platform); only ideas and MIT tables are shared.

See `docs/COMPATIBILITY.md` (included in the source ZIP) for the exact pinned revisions.

## Changelog

- **1.0.0 (second build)** — Tristan's Lib moves to **1.0.0**: both projects now display the same version (mod, library, manifest — no remaining traces of 0.x, including what the launcher's compiler displays); the **turtle helmet emblem** is added to the configuration screen header; the **mod and library logos** are delivered as PNG files ready for the registry.
- **1.0.0** — Enchantment glint; mod manager entry (name, version, description, author, configurable keybind, reset button); official names for both projects (TristanPbty's Armor Hud / Tristan's Lib); the `native_on` gate (a mod disabled in Options > Mods immediately becomes inactive); the actual durability API at the Horizon pin; macro-free helpers so the game's `hook_points_stay_hookable` test passes; the complete staging pipeline executed successfully.
- **0.1.1** — Fixed slot backgrounds (complete `gui/sprites/hud/hotbar` atlas keys for `native_sprite`).
- **0.1.0** — Initial release.
