# KERBAL MISSION CONTROL — Phase 1 Spec (binding)

*Fable, 2026-07-05 night shift. Grounded in [KERBAL-MISSION-CONTROL-SCOUT.md](KERBAL-MISSION-CONTROL-SCOUT.md) (GO-WITH-CAVEATS, all claims verified). Project: gl-0083. Repo home: `game-forge/kerbal-mission-control/` (new subproject beside the Godot bridge — same family of game-RPC harnesses).*

## North star

**Kruz is Program Director. The fleet is the engineering corps.** He states a mission; agents design (template-mutate) the craft, write/configure guidance, fly unmanned tests via kRPC, iterate on failures, and deliver a flight-ready vehicle with a mission report — telemetry plots, failure history, delta-v margins, screenshots. He flies the crewed flagship missions himself. Every unmanned flight is a verified E2E test of an agent-built artifact against real physics, with a proof bundle.

**The deadliest failure here is the same as Orbit's: stale trust.** A mission report must never claim more than telemetry proves.

## Architecture (v1)

```
greenlight queue (missions = items, kind=mission)
        │ dispatch
        ▼
mission_control.py  (the daemon; Python; BelowNormal; headless-spawned)
  ├─ ksp_rig.py      — process supervision: launch KSP windowed/parked,
  │                    watchdog (restart every N missions — the 1.12
  │                    memory-leak doctrine), crash detect, log capture
  ├─ krpc_client.py  — connect-per-mission (never one eternal conn),
  │                    stream telemetry → mission JSONL (flight-recorder
  │                    pattern: ts, alt, apo, peri, stage, resources)
  ├─ craft_forge.py  — TEMPLATE MUTATION ONLY in v1: load .craft
  │                    (ConfigNode parse via taniwha/cfgnode), swap
  │                    engines/fuel/staging params, write variant,
  │                    validate part references against a parts index
  ├─ guidance/       — ascent.py (port of kRPC docs launch-to-orbit
  │                    tutorial, parameterized) + mechjeb.py (KRPC.MechJeb
  │                    driver — verify end-to-end in P1 before depending)
  ├─ verify.py       — mission verdicts from telemetry ONLY:
  │                    orbit achieved = periapsis > 70km (Kerbin),
  │                    crash = situation+parts-count heuristic (harden),
  │                    every verdict carries its evidence rows
  └─ report.py       — Discord mission report: outcome, key numbers,
                       screenshot, cost (wall time + revert count),
                       honest UNKNOWNs; proof bundle → queue entry
```

**Rig placement doctrine:** the KSP window is a **real GUI process** — it runs on the gaming PC ONLY in away/asleep posture (governor-enforced, like cargo builds), parked small/corner, never during games. **Permanent home = the MS-A2 on arrival** (CPU-bound game, iGPU-viable — scout §4). This project is a first-class reason the node exists.

## Phases + verify bars

**P0 — rig (one morning click + one lane).**
- Kruz (or morning me via Steam): install KSP1 (`steam://install/220200`, ~4GB). ← *the only human-ish step*
- Lane: CKAN → kRPC 0.5.4 + KSPCommunityFixes (MANDATORY — memory-leak patch); settings: windowed, simulate-in-background ON, kRPC auto-start + auto-accept, potato graphics; dedicated clean instance (no personal mods).
- *Verify: game boots to menu with kRPC server tab visible; settings file diff committed.*

**P1 — hello world (one evening; the empirical GO/NO-GO).**
- `pip install krpc`; stock Kerbal X to pad via `launch_vessel_from_vab`; connect; **print one live telemetry value from outside the game.**
- Empirical answers required (they gate P2's design): (a) RPC + physics behavior unfocused and minimized; (b) 20× revert-cycle soak — RAM curve with KSPCommunityFixes; (c) KRPC.MechJeb ascent fires or falls back to tutorial guidance.
- *Verify: telemetry line + the three empirical answers written into this spec's ADR section.*

**P2 — first flagship milestone: unmanned orbit, zero human input.**
- Full loop: load save → launch templated craft → ascent → circularize → **periapsis > 70km verified from telemetry** → screenshot + report to Discord → revert/recover → next queue item.
- *Verify: the Discord report exists, its numbers reproduce from the mission JSONL, and NOBODY touched the keyboard. This is the ribbon-cutting.*

**P3+ (post-milestone, Kruz-gated):** mission campaign queue (Mun flyby → landing → return); craft-design tournaments (N template mutations race, best proceeds — judge = delta-v margin + cost); crewed-mission prep briefs for Kruz's flights; MS-A2 migration.

## Hard rails

- Telemetry is the only truth: no verdict without evidence rows; UNKNOWN is a valid verdict.
- Reconnect-per-mission; watchdog restart every N missions (N determined by P1 soak).
- The rig never runs in game/interactive posture on the gaming PC.
- v1 craft = template mutation only; freeform generation is a separate future research item.
- Mission spend is metered like any lane (TPVS: one mission = one unit candidate).

## Decisions for Kruz (morning)

1. **P0 install timing** — say "install ksp" and it happens (or click the Steam button yourself).
2. First mission campaign after orbit: Mun program vs Minmus vs "biggest rocket that doesn't explode" derby.
