# Low-Drain Operating Model — token + machine efficiency doctrine
*Fable, 2026-07-03 (written during Kruz's evening gaming window — API-only
work, zero machine load, per the game contract). Companion to the model
router (fleet/model-router) and FABLE_BURN_PLAN's "post-Fable operating
doc". This is the draft that the Jul 7 harvest day finalizes.*

## A. Today's empirical token profile (from meters.json, est)

- Week to date: fable lane 72.7M io tokens (36.3% of 200M), fleet 12.6M+
  (~3%). Bottom line: **the orchestrator (me) costs ~6x the entire
  worker fleet** despite the fleet doing most keystrokes.
- Current 5h window: opus io 2.08M across 550 calls; **cache reads 96.9M —
  46x the io volume**. Sonnet: 0.67M io across 1,583 calls, 129M cache
  reads. Cache traffic tracks CONTEXT SIZE × TURN COUNT, and is the real
  consumption driver.
- Why the orchestrator is expensive: every background-lane notification
  re-invokes the full main-session context (a very long day = very fat
  context). 550 opus calls in 5h ≈ notification churn + guard-loop chunk
  renewals + interactive turns.

## B. Doctrine — where tokens should go

1. **Fable/Opus = decisions, architecture, verdicts, design, incident
   command.** Never mechanical work, never file spelunking (delegate to
   scouts), never polling.
2. **Sonnet = default worker** (proven today: WP1-4, HERALD D2, diligence,
   sweep — all Sonnet, all shipped). **Haiku = mechanical chores** (STATUS
   refreshes, syncs, prunes). **Codex = audits** (off-Anthropic-budget,
   fail-closed) — the ship-gate reviewer, not a builder.
3. **Notification hygiene** (biggest orchestrator lever found today):
   - Guard/sampler loops must be SCHEDULED-TASK daemons (headroom flight
     recorder), never orchestrator background-command chunks that re-wake
     the fat main context every 9.5 minutes.
   - Lanes report ONCE at completion (already true); avoid mid-lane
     SendMessage chatter unless a directive changes.
   - Batch small ops into single tool calls (fewer turns = fewer cache
     passes over a giant context).
4. **Context hygiene**: headless lane briefs stay self-contained and lean;
   lanes must not load the umbrella CLAUDE.md corpus they don't need
   (greenlight runs already scrub env — extend to context curation).
5. **Machine floor is part of "drain"**: one heavy-build lane at a time,
   BelowNormal everywhere, pre-emptible in seconds
   (feedback_burn_interactivity_floor). Token efficiency and FPS
   protection are the same discipline: right-size every consumer.

## C. llm-benchmark-suite integration — "Router Calibration" (Kruz's call,
2026-07-03: "our model benchmarker project could wrap in somehow")

He's right, and it upgrades both projects:
- **The router today routes by my heuristics** (kind/size → model). That
  table is guesses. **The benchmark suite makes it empirical.**
- **Phase proposal for llm-benchmark-suite** (its first REAL customer):
  build a "fleet task-class eval" — replay ~20 representative greenlight
  tasks from today's history (STATUS refresh, small fix, module build,
  diagnose run, verdict write) against haiku/sonnet/opus with the real
  greenlight verify bars as pass/fail. Output: per-task-class
  {model → pass rate, io tokens, wall time} → an **empirical routing
  table** the router loads from a config file instead of hardcoded rules.
- Loop: router logs every dispatch (id, model, outcome, tokens — already
  in its spec) → benchmark suite ingests outcomes weekly → routing table
  updates → cheapest model that clears the verify bar wins each class.
  Misroutes (fail → retry at higher tier) are the training signal.
- This gives the benchmark project a living product surface (not just a
  leaderboard) and gives the fleet provable lowest-token routing.
  Greenlight proposal to be filed at next work window: "Benchmark suite
  Phase 2: router calibration evals" (needs Kruz !ok).

## D. QA ledger — today's shipped items and their audit state

| Item | Verified by | Outstanding audit |
|---|---|---|
| Orbit WP1 theme+fonts | 8 tests, -j2 checks | Codex gate w/ 0.3.0; visual font proof at integration |
| Orbit WP2 meters reader | 52 tests | Codex gate; flagged: STALE_THRESHOLD re-export path |
| Orbit WP3 zone stack | 78 tests, 0 warnings | Codex gate; NO screenshots yet; refining_impl_trait allow = intentional |
| Orbit WP4 brain bar | 65 tests (14 new) | Codex gate; dead_code allows removed at seam wiring |
| Seam wiring + 0.3.0 ship | NOT DONE (integrator died 2x) | Full Phase D re-run — first heavy lane of next window |
| HERALD D1+D2 merge | 119 tests, rails-checked, PUSHED | PII history retained (accepted, private repo); flaky net test known |
| Magpie diligence merge | 338 tests, rails-checked, PUSHED | none blocking |
| Atlas 5-col usage patch | 20 tests, live-verified, DEPLOYED | atlas-mcp processes hold old binary until restart; HUD binary stale |
| Bridge image support | 24 tests, deployed, loop verified | E2E image round-trip untested with a REAL Kruz phone post |
| Headless sweep | live-verified per task | fleet/headless-sweep branch unmerged (docs+ledger) |
| Cartograaf mockup rev A | Playwright render + shots | Kruz design lock pending (region hues, tab placement, P3 scope) |
| Flight recorder | DIED 3x — partial branch | PRIORITY ONE next window, built solo |
| Model router | DIED 2x — partial branch | second lane next window, after governor |

## E. Standing next-window order (locked)
1. Headroom governor daemon — alone, verified live, gates everything.
2. 0.3.0 integrator re-run (single heavy lane) → Codex gate → package.
3. Model router (light) + router-calibration greenlight proposal.
4. Everything else queues behind, one heavy at a time.
