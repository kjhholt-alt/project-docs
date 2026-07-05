# KERBAL MISSION CONTROL (gl-0083) — Verify-First Scout Report

*Cloud lane, 2026-07-05 (night shift). Companion: KERBAL-MISSION-CONTROL-SPEC.md (Phase-1 spec, built on this).*

**Verdict up front: GO-WITH-CAVEATS.** The stack is real, alive, and battle-tested — **MIT Lincoln Laboratory runs an annual autonomous-agent competition on exactly this architecture** (KSP1 + kRPC + Python), and an MIT team took 2nd place with a pure LLM agent: [arXiv 2404.00413 "Language Models are Spacecraft Operators"](https://arxiv.org/abs/2404.00413). The caveats are operational, not existential.

## 0. Ground truth

- **Steam "Kerbal Space Program" (appid 220200) = KSP1**, final patch 1.12.5 (early 2023, explicitly the last) — **the game is frozen forever, so mod compatibility can never break again.**
- **KSP2 is dead** (studio shut 2024, zombie EA listing). kRPC abandoned its KSP2 branch. Do not touch.
- **Local check: KSP1 is owned but NOT currently installed** (no appmanifest_220200 in A:\Steam or C:\SteamLibrary) → Phase 0 includes a ~4 GB Steam install. Nothing to buy.

## 1. kRPC — ALIVE (and can't rot)

- [krpc/krpc](https://github.com/krpc/krpc): 4,950 commits; **v0.5.4 (June 2024)** fixed memory leaks; supports KSP 1.8+ → covers final 1.12.5 permanently. Install via CKAN/SpaceDock.
- Python client: `pip install krpc` (0.5.4, "Production/Stable").
- **[KRPC.MechJeb v0.7.1](https://github.com/Genhis/KRPC.MechJeb/releases)** (Dec 2024, compatible KSP 1.12 + kRPC 0.5.4) — ascent guidance/node execution via RPC without writing GNC from scratch.
- Honest read: "last release June 2024" for a game frozen in 2023 means *done*, not *dead*.

## 2. Automation reality — no headless, but a solved pattern

- **No headless mode** (KSPDG's own README confirms). Every real project runs a **windowed instance** with kRPC attached. Run windowed (fullscreen self-minimizes), "simulate in background" ON; behavior when fully minimized = Phase-1 empirical test (fallback: small window parked in a corner / dedicated desktop on the node).
- UI-popup killers solved by config: kRPC **auto-start server + auto-accept clients**; `launch_vessel(recover=True)` clears pad blockage; `krpc.paused` (r/w) + `current_game_scene` for stuck-state detection.
- **Time compression**: `warp_to()` — on-rails up to 100,000× for coasts; physics warp ~4× during burns. Near-real-time only during powered flight.
- **Precedents:** [KSPDG (MIT-LL annual challenge)](https://github.com/mit-ll/spacegym-kspdg) · [ARCLab-MIT LLM agent, 2nd place](https://github.com/arclab-mit/kspdg) + [arXiv 2404.00413](https://arxiv.org/abs/2404.00413) · [KSP-NEAT evolved rockets](https://github.com/Yakuzi-PhD/KSP-NEAT) · kRPC's own launch-to-orbit autopilot tutorial.

## 3. Mission loop — every verb is RPC-native (no companion mods needed)

load/save (`load`, `quicksave`) · launch craft file to pad (`launch_vessel(craft_directory, name, launch_site, recover, crew)`) · switch vessels (`active_vessel` writable) · stage/fly (`vessel.control`, `auto_pilot`) · maneuver nodes (`add_node` + MechJeb execution) · full streamable telemetry (`flight()`, `orbit`, `resources`) · revert (`revert_to_launch`) · evidence (`screenshot(path, scale)`, `ut`).
**Crash/success detection: no single RPC** — infer from `vessel.situation` + parts count + orbit params (minor unknown, hardening needed).
**Craft design outside the VAB:** .craft = plain-text ConfigNode; parsers exist ([taniwha/cfgnode](https://github.com/taniwha/cfgnode)); generation precedent thin ([KSP-Optimizer](https://github.com/circumlocutrix/KSP-Optimizer), KSP-NEAT). **v1 doctrine: agents parameterize/mutate TEMPLATE crafts** (engines, fuel, staging) — freeform CAD is a later research lane.

## 4. Failure modes — known, with known mitigations

1. **KSP 1.12 memory leak on repeated reverts/scene changes** (well-documented) → **[KSPCommunityFixes](https://github.com/KSPModdingLibs/KSPCommunityFixes) is MANDATORY day-one** (its MemoryLeaks patch targets exactly this) + orchestrator watchdog restarting KSP every N missions.
2. kRPC streams can die across scene switches → reconnect-per-mission, never one eternal connection.
3. Mod conflicts → dedicated minimal test-rig install (kRPC + KSPCommunityFixes only, via CKAN), separate from any personal install.
4. **Hardware: KSP is CPU/RAM-bound, iGPU-viable** (runs on 2013-era iGPUs; ~1.5GB RAM vanilla) → **the MS-A2 (16C + Radeon 610M) is the right permanent home for the unmanned test rig** — KSP as a background lane on the gaming box collides with game-mode doctrine.
5. **No parallelism in v1** — one windowed instance, serial mission queue (KSPDG's explicit warning). Multi-instance = later experiment.

## 5. Verify-step ladder

- **Phase 0 — rig:** Steam install KSP1 → CKAN → kRPC 0.5.4 + KSPCommunityFixes → windowed, simulate-in-background, kRPC auto-start/auto-accept, potato graphics.
- **Phase 1 — hello world (one evening, empirical GO/NO-GO):** `pip install krpc` → stock Kerbal X to pad via `launch_vessel_from_vab` → connect → **print one live telemetry value from outside the game.** Also answer empirically: unfocused/minimized behavior; 20× revert-cycle stability; RAM curve.
- **Phase 2 — first flagship milestone:** load save → launch templated craft → ascent guidance (kRPC tutorial port or KRPC.MechJeb) → circularize → **verify periapsis > 70 km** → screenshot + telemetry → **mission report to Discord** → revert → next mission. Zero human input.

## Ranked unknowns (all empirical, none architectural)

1. Long-run revert/reload stability (needs a 50-mission soak with the fixes + watchdog)
2. Unfocused/minimized physics + RPC responsiveness
3. Crash/success heuristic robustness (partial breakup, landed-but-dead)
4. Programmatic craft *generation* validity (template mutation safe; freeform unproven — off critical path)
5. KRPC.MechJeb end-to-end freshness (fallback: hand-rolled guidance from the docs tutorial)
6. Multi-instance parallelism (deferred)

**Caveats in one line:** a real game window must run somewhere (no headless), the memory-leak watchdog is mandatory day-one, KSP isn't installed yet, and craft design starts template-based — none of which blocks Phase 1.
