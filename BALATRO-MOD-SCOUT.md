# Balatro Modding Scout Report — 2026-07-04

Research scout on the Balatro modding stack, the scene, and what building our own mod would take. All claims web-verified with cited URLs. *Origin: Kruz's mid-game note 2026-07-05 ("mod the card game... played a Pokemon card mod and loved it"); researched by a cloud lane in the dying quota window. Game guess = Balatro (unconfirmed — see the StS2 note at the end if wrong).*

---

## TL;DR

The Balatro modding stack in mid-2026 is **mature, Lua-based, and unusually agent-friendly** — arguably the best-fit modding target in the entire portfolio. Steamodded (SMODS) 1.0-beta + Lovely injector give a declarative Lua API where a custom joker is ~30 lines of code plus a sprite. Hot-reload exists (DebugPlus), a **JSON-RPC botting API with headless mode and pytest harness already exists** (coder/balatrobot — it literally ships a CLAUDE.md), and the flagship mods (Pokermon: 439+ jokers; Cryptid: base-game-sized content; Multiplayer: real-time PvP) prove every pattern we'd want is possible. **Recommendation: GO** for a weekend S-tier project, with one 30-minute verify-step first.

---

## 1. The stack (2026 state)

**Two layers:**

- **Lovely injector** — a Rust runtime Lua injector for LOVE2D games. On Windows it's a `version.dll` dropped into the game directory; mods declare code patches in `lovely.toml` (pattern, regex, copy, and module patch types). Latest release v0.9.0 (Jan 21, 2026). Open source, works with any LOVE2D game. ([github.com/ethangreen-dev/lovely-injector](https://github.com/ethangreen-dev/lovely-injector))
- **Steamodded (SMODS)** — the modding framework on top. Current release **1.0.0-beta-1814a (June 14, 2026)**, 57 releases, still officially "beta" but the de-facto standard. ([github.com/Steamodded/smods](https://github.com/Steamodded/smods))

**Basis:** Balatro is LOVE2D + Lua. Mods are plain Lua files — no compilation, no SDK install, no engine.

**Install flow (Windows/Steam):** (1) drop Lovely's `version.dll` into the game dir (Steam → Manage → Browse local files); (2) put Steamodded and mods into `%AppData%/Balatro/Mods/`. Steam version only — MS Store build unsupported. ([Installing Steamodded — Windows](https://github.com/Steamodded/smods/wiki/Installing-Steamodded-windows), [TroubleChute guide](https://hub.tcno.co/games/balatro/modding/))

**Mod coexistence:** each mod is its own folder under `Mods/` with a JSON manifest declaring `id`, `prefix` (namespaces all registered object keys, so mods don't collide), `dependencies` with version constraints, and `conflicts`. Load order via `priority`. ([Mod Metadata wiki](https://github.com/Steamodded/smods/wiki/Mod-Metadata)) Distribution: Nexus, Thunderstore, Balatro Mod Manager.

**Safety:**
- **Achievements**: Steamodded disables them by default (re-enableable in config). No multiplayer/anticheat concerns — single-player game.
- **Saves**: back up `%AppData%\Balatro` before modding; uninstall = delete `Mods/` + `version.dll`. Modded runs can pollute unlocks/stats — separate profile or backup is the norm.
- Churn risk: Steamodded's "Better Calc" rework broke pre-2025 mods; API is beta — pin versions. ([Steamodded Miraheze](https://balatromods.miraheze.org/wiki/Steamodded))

---

## 2. The scene — what the flagships prove possible

| Mod | What it is | What it proves |
|---|---|---|
| **Pokermon** (the Pokemon card mod Kruz played) | **439+ Pokemon jokers** with evolution mechanics, custom boss blinds, daily challenge, Safari rarity, full custom art | Themed total-conversion joker ecosystems with new mechanics layered on jokers (evolution = jokers transforming) fully supported ([github.com/InertSteak/Pokermon](https://github.com/InertSteak/Pokermon)) |
| **Cryptid** | 100+ jokers, 20 decks, 20 vouchers, configurable gamesets — near base-game scale | New decks, vouchers, challenges, config UIs all API-supported ([github.com/SpectralPack/Cryptid](https://github.com/SpectralPack/Cryptid)) |
| **Talisman** | Raises score cap to ~10^1000^10, new scoring operators (^Mult, ^Chips) | Mods can rewrite core math/engine behavior, not just add content ([github.com/SpectralPack/Talisman](https://github.com/SpectralPack/Talisman)) |
| **Multiplayer** | Real-time PvP (Attrition/Showdown) | Networking from inside a mod is possible |
| **CozyStats / Divvy's History / JimboStats** | Run-history/analytics mods; JimboStats POSTs run data to an external API | **The run-analyzer/telemetry angle is proven** — full game state readable from Lua and exportable ([github.com/NoxZet/CozyStats](https://github.com/NoxZet/CozyStats), [github.com/JimboStats/jimbo-stats-mod](https://github.com/JimboStats/jimbo-stats-mod)) |

Also: **Joker Forge**, a web GUI that generates SMODS joker code ([jokerforge.jaydchw.com](https://jokerforge.jaydchw.com/)) — useful as a reference generator.

---

## 3. Anatomy of our own mod

```
Mods/OurMod/
├── ourmod.json          # id, name, author[], description, prefix, main_file, version, dependencies
├── main.lua             # entry point
├── assets/1x/Jokers.png # sprite sheet, 71x95 px per card cell
├── assets/2x/Jokers.png # same at 2x (142x190/cell) — both required
└── localization/en-us.lua (optional; loc_txt can live inline)
```

**Per joker:** `SMODS.Atlas{key, path, px, py}` once, then `SMODS.Joker{key, loc_txt, config, rarity, cost, blueprint_compat, atlas, pos}` + a `calculate(self, card, context)` firing on game events (`joker_main`, `scoring`, `end_of_round`...), optional `calc_dollar_bonus`, `add_to_deck`, `in_pool`, `loc_vars`. **~20-40 lines each.** ([SMODS.Joker wiki](https://github.com/Steamodded/smods/wiki/SMODS.Joker), [Your First Mod](https://github.com/Steamodded/smods/wiki/Your-First-Mod))

**Dev loop:** built-in quick restart (hold M / Alt+F5); **DebugPlus** adds console, atlas hot-reload, live re-loading of joker functions with error protection ([github.com/WilsontheWolf/DebugPlus](https://github.com/WilsontheWolf/DebugPlus)).

**Effort tiers:**
- **(S) 5-10 custom jokers + themed art** — 1 weekend. Code is the easy 30%; art (10 cells × 2 resolutions) is the real cost. AI pixel art downscaled to 71x95 is the community shortcut.
- **(M) Themed deck + jokers + vouchers** — 2-3 weekends. Same registration pattern; cost is design/balance iteration.
- **(L) Mechanic mod** (new scoring, transformations, telemetry overlay) — 3-6 weekends. Reading vanilla source + lovely.toml patches; Talisman/Pokermon prove the ceiling.

---

## 4. The Kruz angle — three fitted concepts

**A. "The Fleet" joker pack (S — the weekend pick).** Stoker, Emissary, Orbit, Greenlight, Hermes, REM as jokers whose mechanics mirror their real roles: *Greenlight* — jokers to its right do nothing until Greenlight approves one per blind, then xMult per approved joker. *REM* — end of round, consolidates: destroys your lowest-value joker, permanently upgrades this one. *Orbit* — retriggers (re-verifies) the last scoring card. *Hermes* — copies a random adjacent joker's effect each round. *Emissary* — sent out: leaves the joker row for a blind, returns with a random reward. Deeply personal, funny, exactly S-tier. **Technical unknown: none beyond toolchain hello-world** — every mechanic is a documented `calculate` context or vanilla pattern (Blueprint, Madness).

**B. John Deere / farm-economy pack (S-M).** Combines + implements as jokers; "Harvest" mechanic — jokers accumulate value over antes then cash out (`calc_dollar_bonus`); an "AX02 Overhead Review" boss blind that debuffs your most expensive joker. **Unknown to verify:** `SMODS.Blind` docs are thinner than jokers — keep v1 jokers-only if thin.

**C. BuildForge-for-Balatro: run analyzer/telemetry (M-L, the "our lane" play).** Streams run state (hands, joker lineup, econ curve, score-per-blind) to local JSONL + a BuildForge-style verdict engine ("your build's scaling stalls at ante 6; xMult density too low"). **API question already answered: yes** — CozyStats records every hand; JimboStats ships run data to HTTP from inside the mod; `G.GAME` exposes everything. Aligns with groundtruth/BuildForge doctrine and could feed the games-hub. **Unknown to verify:** build the capture layer vs consume CozyStats/balatrobot serialization — read both repos first.

---

## 5. AI-assisted mod dev — unusually agent-friendly

- **Plain Lua, declarative, no build step** — agents write/lint whole mods; syntax-check Lua pre-launch (house doctrine).
- **Hot reload** (DebugPlus) — sub-minute iteration.
- **A headless test harness already exists**: [coder/balatrobot](https://github.com/coder/balatrobot) runs Balatro `--headless --fast`, exposes JSON-RPC 2.0 HTTP (port 12346, 20+ endpoints: play hand, buy, advance round), runs **pytest suites in parallel** with auto-managed instances — and ships a CLAUDE.md. An agent can implement a joker, boot a headless run, force it into play, and assert on scoring — a real verify loop with no human at the keyboard.
- Matches the GameForge/deck-of-iron pattern: agent builds + verifies headless; Kruz's irreplaceable job is the *feel* pass.

**Slay the Spire 2 note (if the game guess is wrong):** StS2 got official Workshop support only 2026-06-19 (v0.107.1), loader self-described "barebones," Unity/C# not Lua — younger docs, heavier toolchain. Balatro is the better first mod either way; Decksmith already covers the StS2 lane.

---

## Verdict: GO (weekend project, S-tier scope)

**Recommended first project:** Concept A, "The Fleet" joker pack — 5-8 jokers, inline loc_txt, one atlas.

**Exact first verify-step (api-check doctrine):**
1. Back up `%AppData%\Balatro`.
2. Install Lovely `version.dll` + Steamodded 1.0.0-beta per the [Windows install wiki](https://github.com/Steamodded/smods/wiki/Installing-Steamodded-windows).
3. Hello-world joker: one folder, one manifest, one `main.lua` with a single `SMODS.Joker` ("+4 Mult, always"), reusing a **vanilla atlas position** (no custom art for the smoke test).
4. Launch, confirm the mod badge in the Mods menu, spawn via debug menu, play one hand, confirm +4 Mult fires.
5. Then: DebugPlus hot-reload check, then scope the real pack.

If steps 3-4 take >1 hour, beta-API version drift is the culprit — pin to the exact Steamodded release the wiki examples target before debugging anything else.

*(Full source list inline above.)*
