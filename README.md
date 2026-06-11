# RTS Camera Universal

**An all-in-one RTS / Total War–style battle command suite for Mount & Blade II: Bannerlord — built to work across game versions and to play nicely with overhaul mods (The Old Realms / Warhammer, War Sail DLC).**

🇷🇺 [Документация на русском](README.ru.md) · 📜 [Changelog](CHANGELOG.md)

---

## What it is

A single module that bundles three mods that previously had to be installed separately, plus original fixes and features:

- **RTS Camera** — free top‑down camera in battle (lift off, look down, command like a strategy game).
- **Command System** — improved formation orders: click‑to‑move, order queue, visual preview (arrows / ghosts / formation outlines), facing, volley control, troop highlighting.
- **Battle Mini Map** — a real minimap in battle with unit positions and terrain.

One folder, **one checkbox in the launcher**, shared libraries de‑duplicated. The three DLLs stay separate internally so each can be maintained independently.

## Features

- Free camera toggle (default **F10**); WASD + mouse to fly; configurable speed/height.
- Take control of a soldier after your hero is injured (default **E** to focus/control).
- Battle time control: **pause**, **slow motion**, **fast forward** *(hotkeys unbound by default — assign them in the in‑battle menu)*.
- In‑battle hotkey re‑binding with **conflict protection** — a key already used by another action cannot be assigned twice.
- Formation commanding: move/charge/advance/fallback, formations (line, shield wall, circle, square, …), facing, volley/auto‑volley, **order queue** with on‑ground visual preview.
- Troop highlighting (outline or ground marker), movement‑target markers.
- **Battle minimap** (toggle key in the mini‑map menu): unit positions, terrain heights, configurable size/position/opacity/scale.
- Campaign integration: free‑camera range scales with **Tactics** and **Scouting** skills.
- War Sail (Naval) support: command and pilot ships from the RTS camera.
- The Old Realms (TOR / Warhammer) compatibility.
- File log for diagnostics (see below).

## Why this build, not the original?

This is a port/compilation of lzh's open‑source mods (MIT). Here is what it adds **over the originals**:

- **🎯 One mod for every game version — Bannerlord 1.2.x, 1.3.15 and 1.4.5.** The originals are version‑specific (you hunt for the matching re‑upload). This build detects your game version at launch and loads the right code + assets automatically — the Battle Mini Map included on all three.
- **🧩 All‑in‑one, one launcher entry.** RTS Camera + Command System + Battle Mini Map merged into a single module with shared libraries de‑duplicated — the originals are separate downloads.
- **🌍 Better localization.** The originals shipped English, 中文 and Français but **no Russian** — this build adds a full **Русский** translation (367 strings) and also **improves the French** (corrected phrasing + the new dialogs translated).
- **🧙 The Old Realms (Warhammer) crash fixes.** Fixed the **magic‑cast crash** (casting under RTS‑camera control routed into TOR's AI cast path → `NullReferenceException`, crashing the battle, notably on battle end), and the **per‑frame crash spam** from TOR's custom Artillery formation index (`ArgumentOutOfRange`).
- **🐛 Original bugs fixed.** Division‑by‑zero/`NaN` in the ammo‑ratio calc, a broken mouse‑over formation highlight, a camera‑bearing reset bug, minimap texture/config issues, and a wrong‑target‑framework load crash (now built for net472).
- **🛡️ Crash‑proof across versions.** Every Harmony patch is applied in isolation and logged by name — a patch that a newer game version changed degrades and logs instead of taking the whole mod (or the game) down with it (e.g. on 1.4.5). A code‑audit pass further unified the shared library (removing a 1.2‑only crash in the in‑battle hotkey config), added per‑frame null/disposed guards across the camera, command and minimap paths, and fixed several cross‑battle handler leaks.
- **🔔 Graceful "no Harmony" handling.** If **Bannerlord.Harmony** isn't enabled, the game **does NOT crash** — at the main menu you get a clear dialog (in your game's language) with **direct download links for your exact game version** (Steam Workshop / Nexus), Cancel and "don't show again" options, plus a hint to put Harmony at the top of the load order.

## Requirements

- **Mount & Blade II: Bannerlord 1.2.x – 1.4.x** (built for .NET Framework / net472, matching the game).
- **Bannerlord.Harmony** — the build depends on your game version, see the [“Which Harmony to install”](#which-harmony-to-install-depends-on-your-game-version) table below.
- *Optional:* War Sail DLC, The Old Realms (TOR/TOW) — extra features activate automatically when present.

## Installation

1. Install **Bannerlord.Harmony**.
2. Copy the `RTSCameraUniversal` folder into your game's `Modules` folder:
   `...\Mount & Blade II Bannerlord\Modules\`
3. In the launcher, enable **Bannerlord.Harmony**, the native modules, your overhaul (e.g. TOR), and finally **RTS Camera Universal** (load it after the overhaul).

### Which Harmony to install (depends on your game version)

> ⚠️ **Bannerlord.Harmony** has two builds split by game version — install the one matching YOUR Bannerlord:

| Your Bannerlord | Which Harmony | Steam Workshop | Nexus (no Steam) |
|---|---|---|---|
| **1.2.x** (1.2.12 and older) | Harmony **v1.0.0 – v1.2.12** | [Workshop 3613449471](https://steamcommunity.com/workshop/filedetails/?id=3613449471) | [Harmony, mod 2006](https://www.nexusmods.com/mountandblade2bannerlord/mods/2006?tab=files) → **Optional files** section |
| **1.3.x** (e.g. 1.3.15) | Harmony (current) | [Workshop 2859188632](https://steamcommunity.com/workshop/filedetails/?id=2859188632) | [Harmony, mod 2006](https://www.nexusmods.com/mountandblade2bannerlord/mods/2006?tab=files) → main file |
| **1.4.x** (e.g. 1.4.5) | Harmony (current) | [Workshop 2859188632](https://steamcommunity.com/workshop/filedetails/?id=2859188632) | [Harmony, mod 2006](https://www.nexusmods.com/mountandblade2bannerlord/mods/2006?tab=files) → main file |

If **Bannerlord.Harmony** is not enabled, entering the mod shows a dialog with a **“Download”** button that opens the correct link for your exact game version in your browser.

## Usage / hotkeys

| Action | Default key |
|---|---|
| Toggle free (RTS) camera | **F10** |
| Move camera | **WASD** + mouse (up/down = jump/crouch keys) |
| Focus / control a soldier | **E** |
| Open the mod menu (settings & hotkeys) | mod "Open Menu" key (configure in‑battle) |
| Pause / Slow motion / Fast forward | **unbound** — set in the menu |
| Toggle minimap | set in the Battle Mini Map menu |

In free camera, select a formation and click the ground to move, or click an enemy to attack. Hold the command‑queue key while ordering to queue multiple orders.

### Directing artillery (The Old Realms)
1. Toggle the RTS camera (F10) and select the **Artillery** formation.
2. Target an enemy formation (Select‑Target‑for‑Command / attack a formation).
3. Set the artillery formation to **Fire at Will** — the crews aim and fire at your chosen formation (TOR's own ballistics). **Hold Fire** stops them.

## Diagnostics

A rolling log is written to:
`Documents\Mount and Blade II Bannerlord\Configs\RTSCamera\RTSCameraUniversal.log`
It records mod messages, caught exceptions, and compatibility‑guard status (e.g. TOR artillery / ability‑cast guards).

## Credits & license

This project is a cross‑version **compilation and port** of open‑source Bannerlord mods. It is **not** original work — full credit goes to the original author.

**Original author:** **Li Zhenhuan** (a.k.a. **lzh** / **LiZhenhuan1019**), with contributions published under **Lkoinw**.

**Sources we built on** (each **MIT licensed**, `Copyright (c) 2020 Li Zhenhuan (lizhenhuan1019@qq.com)`):
- [RTSCamera](https://github.com/lzh-mb-mod/RTSCamera) — the RTS camera + Command System.
- [BattleMiniMap](https://github.com/lzh-mb-mod/BattleMiniMap) — the battle mini‑map (the 1.2 build uses upstream tag `release-v3.0.0`).
- [MissionLibrary](https://github.com/lzh-mb-mod/MissionLibrary) — the shared library.

This project redistributes and adapts them under the **same MIT license**; the original copyright and license text are preserved in [LICENSE](LICENSE). The Old Realms compatibility references the TOR source for accuracy.

See [CHANGELOG.md](CHANGELOG.md) for version history.
