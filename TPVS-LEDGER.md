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
for classes with thin traffic. Harness upgrade (checkpointed replay +
passive collector) lands at next work window; spec fields already emitted.

*Method: lane tokens = subagent io reported at completion; orchestrator =
meters.json fable-lane delta over the day; "verified unit" = merged/
deployed change that cleared its verify bar. Estimates, honestly labeled.*
