# Changelog

All notable changes to **RTS Camera Universal** are documented here.
Format based on [Keep a Changelog](https://keepachangelog.com/). 🇷🇺 [Версия на русском](CHANGELOG.ru.md)

## [1.1.2] — 2026-06-13

In‑game build **1.1.2.15**. The universal build matured into **one download that runs on Bannerlord 1.2.x, 1.3.15 and 1.4.5**, auto‑selecting the right code, GUI and assets at runtime — plus a wave of crash fixes (order‑UI brushes, vanilla "empty column", The Old Realms spell‑cast, formation placement) and a clean, crash‑free first‑launch setup on 1.3/1.4.

> The launcher‑visible module version stays frozen at **v1.1.2** on purpose (a changed module version makes the game re‑ask about every old save and can break heavily‑modded saves such as The Old Realms). Build tracking lives in the in‑game build number (shown in the battle menu) and in this changelog.

### Added
- **One single universal download for Bannerlord 1.2 / 1.3 / 1.4.** A version‑neutral core loader detects the game version at launch and loads the matching payload, GUI / prefabs and asset package. The version‑specific order‑UI prefabs ship side by side under `GUI/Prefabs/Versioned/`. The mesh package (`pack0.tpac`) ships in the 1.2‑safe variant (which every game version can read); on 1.3.15/1.4.5 the loader installs the version‑matched package and asks for one restart on first launch (see *First‑launch setup* below). One launcher checkbox; no per‑version downloads.
- **Battle Mini Map on all three versions, loaded by the universal loader.** 1.3/1.4 use the current map; 1.2 uses the 1.2‑compatible map (pre‑`ImageDrawObject`). Its UI widgets are registered into the engine at runtime per game version — no separate launcher entry.
- **Guard against running the standalone originals alongside the suite.** If the original *RTS Camera*, *Command System* or *Battle Mini Map* are enabled together with RTS Camera Universal (which already includes them), the suite steps aside instead of double‑applying every patch, hotkey and UI element, and a localized popup at the main menu lists the duplicates.
- **Hotkey conflict protection.** The in‑battle key‑binding menu rejects assigning a key (or combo) already used by another action — across all hotkey categories — and shows which action holds it. Exact duplicate combos already present in saved configs are dropped on load.
- **In‑mod build number** shown on entering the mod (separate from the frozen module version).
- **Russian and French** localization on Bannerlord 1.2 (previously English + Chinese only).
- **File logging** to `Documents\…\Configs\RTSCamera\RTSCameraUniversal.log` (mod messages, caught exceptions, compatibility‑guard status).

### Fixed
- **First‑launch setup on Bannerlord 1.3/1.4 — a one‑time restart instead of a crash.** The engine loads the module's mesh package before any mod code runs, so a fresh 1.3.15/1.4.5 install starts on the 1.2‑safe package that lacks this version's order/marker meshes. Rather than risk a crash, the mod stays completely inert on that first setup launch (no patches applied — pure vanilla) and shows a single‑button **"Restart Bannerlord"** dialog; after you restart, the version‑matched package is in place and the mod loads fully. This happens once after installing or updating; Bannerlord 1.2 needs no restart.
- **Bannerlord 1.2 — troops no longer permanently highlighted.** On 1.2 the vanilla "Show Indicators" key could read as held down for the entire battle, force‑highlighting every formation. The mod now ignores a key that is "down" from the first tick and never releases, so highlighting follows the key as intended. (1.3.15/1.4.5 were unaffected.)
- **Order‑UI brush crash on every game version.** The order‑UI prefabs measure brush sprite sizes via `<Constant>` definitions; if a UI‑overhaul mod overrides or removes the native brushes they point at, the engine crashed with a `FormatException` while loading the order UI. Two‑layer fix on all versions: the prefab constants now measure the mod's **own** protected brush copies (`RTSCamera.*`), and a global guard on the engine's constant resolver turns any remaining failure of this kind into a logged "0" instead of a crash.
- **Vanilla "empty column" crash class closed (Bannerlord 1.3/1.4).** A column formation keeps a follow‑the‑leader list of its lead‑file soldiers; vanilla code inserts the order position into that list and unconditionally reads the second element. If a column‑arranged formation is wiped out (or transferred out) and reinforcements are later assigned to it, the vanilla reinforcement spawn asks the empty column for positions and the game crashes inside the mission tick. Closed three ways: a global guard returns "no position" for an empty column (so every caller, vanilla or other mods, falls back to the default spawn point); a formation that empties while arranged as Column is reset to Line; and the command queue refuses to apply Column to an empty formation. (Bannerlord 1.2's column code does not have this bug.)
- **The Old Realms — spell cast from the free camera no longer crashes, on every game version including 1.2.** Casting while the RTS camera held the hero under AI control routed into TOR's AI cast path and threw a `NullReferenceException` (`Ability.GetSpawnFrame` → `CalculateAICastMatrixFrame`), crashing the mission (notably on battle end). The player's cast frame is now used instead — the fix is in both the 1.2 and 1.3/1.4 payloads (TOR's primary version is 1.2).
- **The Old Realms — per‑frame crash spam** from TOR's custom **Artillery** formation index exceeding the vanilla formation count (`ArgumentOutOfRangeException`): bounds checks corrected and the index list grows on demand.
- **Formation placement / multi‑formation order crash.** Selecting several formations and issuing a placement or drag‑line order could feed an invalid (NaN / out‑of‑world) position into the engine's navmesh and arrangement code, which aborted with an absurd native memory request. Positions are now validated at the native boundary — an invalid candidate is skipped and logged instead of corrupting engine state.
- **Original‑mod bugs fixed:** division‑by‑zero / `NaN` in the remaining‑ammo ratio; the mouse‑over formation highlight was discarded; a camera‑bearing reset bug; minimap texture/config issues; a wrong‑target‑framework load crash (now built for net472); a formation order‑clear bug that stopped after the first selected formation; the "significant mounted units" override that never wrote its result; and hidden order‑rotation markers that were never hidden.
- **Localization leaks** — the Helmsman‑detected warning, the naval / War Sails prompts, and the minimap "reset all keys" dialog are translated (RU / FR / CN) instead of leaking English; a broken English/Chinese ship‑capture line was corrected.

### Changed
- **Crash‑proofing at the engine boundary.** Every position, direction, formation width and mesh the mod feeds into the engine is validated first; an invalid value is skipped and logged (`[guard] …`) instead of corrupting native engine state, which is otherwise an uncatchable hard crash. Every Harmony patch is applied in isolation and logged by name, so a patch broken by a newer game version degrades alone (on 1.4.5 only two naval patches degrade gracefully — ships stay controllable). The Bannerlord 1.2 payload additionally runs every engine callback (mission / agent / UI / order hooks) behind a failure breaker that disables only the affected feature after repeated errors, then re‑arms next mission.
- **Optional launcher dependencies.** *CustomBattle*, *StoryMode*, *Sandbox* and *SandBoxCore* are now `optional` in the manifest — the load order is still enforced when present, but the module also loads on repacks and total conversions where those native modules are stripped. None of the mod's code requires those module assemblies.
- **Graceful "no Harmony" handling.** If **Bannerlord.Harmony** isn't enabled, the game does not crash — the main menu shows a clear dialog (in the game's language) with direct download links for the exact game version (Steam Workshop / Nexus), Cancel and "don't show again" options, and a hint to put Harmony at the top of the load order.
- Trimmed bundled libraries to only what is used.

### Compatibility
- One build runs on Bannerlord **1.2.x / 1.3.15 / 1.4.5**. War Sail (Naval DLC) and The Old Realms (TOR/TOW) supported when present.

---

## [1.0.0] — 2026-06-05

First release of the unified suite. Based on RTSCamera **v5.3.26** and BattleMiniMap **v3.1.3** by lzh / Lkoinw (MIT).

### Added
- **Single merged module** "RTS Camera Universal" — one launcher checkbox loading RTS Camera + Command System + Battle Mini Map, with shared libraries de‑duplicated.
- **Full Russian localization** (367 strings across all four string files) — no Russian translation existed upstream.
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
- **Branding**: all on‑screen messages and dialog titles now use the unified prefix **"RTS Camera Universal:"**.

### Compatibility
- Bannerlord 1.3.13 – 1.3.15. War Sail (Naval DLC) and The Old Realms (TOR/TOW) supported when present.

---

*Earlier history belongs to the upstream projects: see RTSCamera and BattleMiniMap by lzh-mb-mod.*
