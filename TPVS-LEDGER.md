# TPVS Ledger — tokens per verified shipped unit
*The north star's scoreboard (Kruz !ok 2026-07-03). Day zero baseline,
computed from actual lane telemetry on the first fully-instrumented day.
Update cadence: per burn day. Method notes at bottom.*

## 2026-07-03 — baseline day (io tokens, est)

### Worker lanes (Sonnet unless noted) — what shipped per token

| Lane | Tokens | Verified shipped unit | Verify bar |
|---|---:|---|---|
| Orbit WP2 meters reader | 105,605 | orbit-ops module, 23 tests | tests green, merged |
| Orbit WP4 brain bar (v3) | 102,437 | atlas wiring, 14 new tests | 65 tests, merged |
| Orbit WP3 zone stack (v3) | 129,988 | zones.rs + posture rebuild, 27 tests | 78 tests, 0 warn, merged |
| Orbit WP1 theme+fonts | 192,150 | amber palette + bundled Plex, 8 tests | tests + checks, merged |
| HERALD D2 artifacts | 119,896 | generator + 25 tests + real bugfix | 113-test suite, merged+pushed |
| Magpie diligence | 213,555 | dossier + helper + reconcile merge | 338 tests, merged+pushed |
| Headless sweep (v3) | 133,603 | 150-task audit + fix, live-verified | schtasks run + artifact proof |
| Atlas usage patch (v2) | 67,634 | 5-col logging, deployed live | 20 tests + live 5-col line |
| Bridge image support | 89,963 | attachments e2e, 24 tests | suite + deploy + loop check |
| Branch merger (v2) | 62,490 | 2 repos verified+merged | 457 tests re-run, rails checks |
| Atlas graph extract (v2) | 80,079 | 4 datasets + derivability verdict | source-line citations |
| swe-agent P2 review | 70,144 | independent verified verdict | reproduced 104-test run |
| Fleet scout / mapper | 180,273 | morning intel + codebase map | consumed by all WP lanes |
| **Fleet total (13 lanes)** | **~1.55M** | **~13 verified units** | **≈ 119k tokens/unit** |

### The orchestrator (Fable/Opus)

~6.9M io tokens today (weekly meter 65.8M→72.7M across the day) — specs,
briefs, integration, incident command, and (the dominant slice) cache-heavy
notification turns. **≈ 530k orchestrator tokens per shipped unit — 4.5x
the worker cost of the unit itself.** Matches the week-scale 6x finding.

### Day-zero verdicts
1. **The ratio's biggest lever is orchestrator overhead, not worker
   choice.** Cutting my per-unit overhead in half beats routing every
   sonnet task to haiku (~10x smaller savings).
2. Concrete orchestrator diet (already begun tonight): scheduled-task
   guards instead of self-waking loops; batch tool calls; lanes report
   once; no polling. Target: orchestrator ≤ 2x worker cost per unit
   within a week — watch this ledger.
3. First model-tier datapoint: chore-S is haiku-viable at 1,186 tokens
   (live judge-verified). Routing table now governs dispatch when present.
4. Killed-attempt overhead (WP3 v1/v2, recorder v1-v3, etc. — sleep/lag
   casualties) is real but unmeasured this day; the flight recorder +
   crash-resilient lanes exist to shrink it. Track from day 1.

### Passive calibration (design decision, 2026-07-04 00:xx)
Replay-based calibration spends tokens to measure (and its process
drumbeat lagged Kruz's game — killed mid-run). The better instrument was
under our feet: **the router already logs (id, model, profile, reason) per
dispatch, and the queue records outcome + verify result.** Passive mode =
join dispatch log × outcomes × per-lane token telemetry → the same
routing-table.json, free, from live traffic, forever. Replay mode remains
for classes with thin traffic.

**Shipped (gl-0044, 2026-07-04):** the harness upgrade landed —
checkpointed `replay` (every `ReplayRecord` fsynced to `--records-out` the
instant it completes, `--resume` skips finished `(sample, model)` pairs)
and a `passive` subcommand that joins the dispatch log with queue outcomes,
zero token spend, method-labeled `passive-live-traffic`. 35 new offline
tests, 135 total green. Re-ran the full 17-sample x {haiku, sonnet} replay
live with checkpointing on: it hit a real 240s timeout under fleet
contention and recorded it instead of dying — proof the v1 failure mode is
fixed. 15/34 pairs landed this session (5 classes); all honestly flagged
`no_clear_pass` at n=1-2/class (expected day-one noise, not a bug).
Installed to `~/.operator/greenlight/routing-table.json`; confirmed
`route_model()` loads it and correctly falls through flagged entries to
heuristics. Passive mode is thin today too — only 3 dispatch-log lines
carry the new model=/profile=/reason= fields (hours old) and 1 has reached
a terminal outcome — but the join is proven and free from here on.

## 2026-07-04 — overnight waves (the full-send night)

| Lane | Tokens | Verified shipped unit |
|---|---:|---|
| 0.3.0 integrator (v4) | 114,895 | seam wiring + shots, 506 tests |
| 0.3.0 Codex ship-gate | 65,855 | SHIP verdict, 5 areas |
| 0.3.0 packager | 129,443 | vpk cut + INSTALLED + STATUS |
| Flight recorder (v6) | 239,826 | recorder shipped, 107 tests |
| Atlas graph export | 117,763 | contract deployed live |
| Knowledge reader | 120,651 | orbit-ops reader, 26 tests |
| **Cartograaf map (Lane C)** | **539,139** | the BRAIN tab, 581 tests |
| Queue triage | 135,682 | 8 ghosts cleared + 3 proposals |
| **Night total (8 lanes)** | **~1.46M** | **~8 units ≈ 183k/unit** |

Night verdicts: (1) the L-size flagship unit (Cartograaf) costs ~4.5x a
normal unit — flagships are their own TPVS class; excluding it the night
ran ~132k/unit, near day-zero's 119k. (2) Crash overhead persists (v3/v5
died to a host crash mid-wave; salvage discipline held the loss to
near-zero because everything commits granularly — the doctrine pays).
(3) The routing table went from idea → installed → governing dispatch in
under 24h; passive mode makes it self-improving from here. (4) Two render
bugs were caught ONLY by screenshot verification, never by 581 green
tests — visual verify bars are load-bearing, keep paying for them.

*Method: lane tokens = subagent io reported at completion; orchestrator =
meters.json fable-lane delta over the day; "verified unit" = merged/
deployed change that cleared its verify bar. Estimates, honestly labeled.*
