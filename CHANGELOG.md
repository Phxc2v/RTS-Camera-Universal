# Changelog

All notable changes to **RTS Camera Universal** are documented here.
Format based on [Keep a Changelog](https://keepachangelog.com/). 🇷🇺 [Версия на русском](CHANGELOG.ru.md)

## [Unreleased] — working build 1.1.0.9 — 2026-06-11

Cross‑version universal release: **one module now runs on Bannerlord 1.2.x, 1.3.15 and 1.4.5**, auto‑selecting the right code and assets at runtime.

### Added — build 1.1.0.9
- **Guard against running the standalone originals alongside the suite.** If the original *RTS Camera*, *RTS Camera Command System* or *Battle Mini Map* mods are enabled together with RTS Camera Universal (which already includes them), the suite now steps aside instead of double‑applying every patch, hotkey and UI element: the originals run alone and a localized popup at the main menu lists the duplicates and asks to disable them.

### Changed — build 1.1.0.9
- **Module version is now permanent: `v1.7.0`.** The launcher‑visible version will never change again — a changed module version makes the game re‑ask about every old save ("modules changed") and can break heavily‑modded saves (e.g. The Old Realms). (Three‑segment on purpose: a four‑segment manifest version breaks the 1.3.15 launcher.) Actual build tracking stays in the in‑game build number (shown in the battle menu) and this changelog.

### Fixed — build 1.1.0.8
- **Order‑UI crash at battle start when another mod breaks native UI brushes.** The order‑UI prefabs measure brush sprite sizes via `<Constant>` definitions; if a UI‑overhaul mod overrides or removes the native brushes they pointed at (`Order.Item.Background`, `Order.Troop.Icon`, `Mission.Radial.Item.*`, …), the engine crashed with `FormatException` while loading the order UI (seen in the wild with the original RTSCamera 5.3.7). Two‑layer fix: (1) the prefab constants now measure the mod's **own** protected copies of those brushes (`RTSCamera.*` in `GUI/Brushes/RTSCameraConstantBrushes.xml`), which other mods cannot override; (2) a global crash guard on the engine's constant resolver turns any remaining failure of this kind — including in other mods' prefabs — into a logged "0" instead of a crash.

### Changed — build 1.1.0.7
- **One identical folder for every game version — asset unification completed.** A single download now works on 1.2 / 1.3 / 1.4 as is: the version‑specific order‑UI prefabs ship side by side under `GUI/Prefabs/Versioned/` and the loader points the engine at the right set for the running game version at startup; the mesh package (`pack0.tpac`) ships in the 1.2‑safe variant and the loader swaps in the matching version's package before the engine reads it. Language files are a single unified superset. No per‑version downloads, no differing installs.

### Added — build 1.1.0.6
- **Hotkey conflict protection.** The in‑battle key‑binding menu now rejects assigning a key (or key combo) that is already used by another action — across all hotkey categories of the suite — and shows which action holds it. Previously a double‑bound key silently fired both actions at once (e.g. a key bound to both *Fast forward* and *Increase camera speed* compounded the camera speed ×1.25 on every press). Exact duplicate combos already present in saved configs are dropped automatically on load.

### Changed — build 1.1.0.6
- **Battle Mini Map is now loaded and registered entirely by the universal loader** — its UI widget types are injected into the engine's widget registry at runtime, per game version, instead of being declared as a separate engine submodule. A step toward a fully byte‑identical module folder across all game versions. Includes a fix for a Bannerlord 1.2 silent crash at battle start that the new registration scheme initially caused.

### Fixed — code‑audit pass (build 1.1.0.1)
- **1.2 crash eliminated** — the shared mission‑library had drifted into two copies; the stale one called an API absent on Bannerlord 1.2 (`GetAllChildrenRecursive`) and crashed when opening the in‑battle hotkey config. The copies are now unified.
- **Per‑frame crash guards** — minimap tick / finalize, map‑item render (null brush), order‑drawing prefix, and a TOR career hook no longer dereference null/disposed engine objects; they degrade gracefully per the never‑crash rule.
- **Formation order‑clear bug** — clearing Fire / Riding / Volley orders on several selected formations stopped after the first one (early `return` instead of `continue`); now clears all.
- **Cavalry spacing override** — the "significant mounted units" override never wrote its result, so the configured threshold was ignored; fixed.
- **Hidden order‑rotation markers** — an inverted null check meant non‑selected rotation gizmos were never hidden; fixed.
- **Memory / handle leaks** — unsubscribed team & formation event handlers that persisted across battles, plus unreleased UI sprite categories, are now cleaned up.
- **Localization** — the Helmsman‑detected warning, the naval / War Sails prompts, and the minimap "reset all keys" dialog are now translated (RU / FR / CN) instead of leaking English; a broken English/Chinese ship‑capture line was corrected.

### Added
- **Universal single module** for Bannerlord 1.2 / 1.3 / 1.4 — a version‑neutral core loader detects the game version and loads the matching payload plus version‑matched GUI / prefabs / asset packages. The wrong‑version assemblies are never touched, so there is no type‑load crash on 1.2. One launcher checkbox, as before.
- **Bannerlord 1.2 support** — full parity: free camera, command system, order arrows, formation unit‑type icons, clicking enemy formations, in‑battle menu (L).
- **Battle Mini Map on all three versions** — 1.3/1.4 use the current map; 1.2 uses the 1.2‑compatible map (pre‑`ImageDrawObject`). Still one module, no extra launcher entry.
- **TOR artillery RTS fire‑control on 1.2** — player‑directed artillery targeting (ported; a no‑op when The Old Realms isn't loaded).
- **Russian and French** localization on 1.2 (previously English + Chinese only).
- **In‑mod build version** shown on entering the mod (separate from the frozen module version).

### Fixed
- 1.2 startup crashes (incompatible mesh package / `VisualOrder` type‑load) and an agent‑spawn `AccessViolation` from a missing marker mesh — now guarded.
- Skill‑effects `NullReferenceException`, the in‑battle **L menu** crash, and on‑screen "Text … doesn't exist" errors.
- Harmony‑missing popup now shows in ONE language (the game's), at the main menu (not over the splash), with Nexus / Steam / Cancel actions and a "don't show again" option; clear "not installed → download" vs "disabled → enable" wording and a "put Harmony at the top of the load order" hint.

### Changed
- **Stability hardening** — every Harmony patch is applied in isolation and logged by name, so a single version‑incompatible patch can never abort the rest or crash the game; risky native calls are validity‑checked first. On 1.4.5 only two naval patches degrade gracefully (ships remain controllable).
- Trimmed bundled libraries to only what is used.

### Compatibility
- One build runs on Bannerlord **1.2.x / 1.3.15 / 1.4.5**.

---

## [1.0.0] — 2026-06-05

First release of the unified suite. Based on RTSCamera **v5.3.26** and BattleMiniMap **v3.1.3** by lzh / Lkoinw (MIT).

### Added
- **Single merged module** "RTS Camera Universal" — one launcher checkbox loading RTS Camera + Command System + Battle Mini Map, with shared libraries de‑duplicated.
- **Full Russian localization** (367 strings across all four string files) — no Russian translation existed upstream.
- **The Old Realms — artillery fire‑control** *(experimental)*: direct TOR cannons to fire at a chosen enemy formation from the RTS camera, reusing TOR's own ballistics. Gated by the formation's Fire at Will / Hold Fire order.
- **File logging** to `Documents\...\Configs\RTSCamera\RTSCameraUniversal.log` (mod messages, exceptions, compatibility‑guard status) for diagnostics.
- Minimap initialization diagnostics.

### Fixed
- **The Old Realms — magic‑cast crash**: casting while the RTS camera held the hero under AI control routed into TOR's AI cast path and threw a NullReferenceException (`Ability.GetSpawnFrame` → `CalculateAICastMatrixFrame`), crashing the mission (notably on battle end). Now the player's cast frame is used instead.
- **Command System — crash spam** in TOR battles: `FormationColorSubLogicV2` threw `ArgumentOutOfRangeException` every frame because TOR's custom **Artillery** formation index exceeds the vanilla formation count. Bounds checks corrected and the index list now grows on demand.
- **Command System — division by zero** producing `NaN` in remaining‑ammo ratio for formations with no ammo‑bearing units.
- **Command System — mouse‑over formation** was never treated as highlighted (discarded LINQ `Append` result).
- **Camera — bearing reset bug**: an operator‑precedence error could zero the camera bearing if a reflected field failed to resolve.
- **Camera — unguarded Naval reflection** replaced with the existing safe accessor.
- **Mini Map — texture buffer** now uses the exact byte array instead of an oversized buffer.
- **Mini Map — config**: the Naval scale‑reduction setting was silently lost on load; now preserved. Inactive `SimpleMiniMap` range init corrected.

### Changed
- **Built for net472** to match Bannerlord 1.3.x runtime (fixes a `System.Runtime 6.0.0.0` load crash from a wrong target framework).
- **Version gate** set to v1.3.15.
- **Branding**: all on‑screen messages and dialog titles now use the unified prefix **"RTS Camera Universal:"**.

### Compatibility
- Bannerlord 1.3.13 – 1.3.15. War Sail (Naval DLC) and The Old Realms (TOR/TOW) supported when present.

---

*Earlier history belongs to the upstream projects: see RTSCamera and BattleMiniMap by lzh-mb-mod.*
