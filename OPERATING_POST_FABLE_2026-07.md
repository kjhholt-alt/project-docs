# Operating Post-Fable — 2026-07-06

Written on Fable's final session, per the burn plan's harvest mandate.
This is the runbook for the fleet without a Mythos-class model in the
loop. Sonnet 5 is the daily driver, Opus 4.8 the architect-on-demand,
Haiku the chore runner. Nothing here is aspirational — every system named
shipped and verified this week.

## What changed this week (machinery inventory + prove-it commands)

| System | What it does | Prove it |
|---|---|---|
| Size-tiered run budgets | S/M/L = 1/2/4h; killed the flat-1h run-killer (14 casualties recovered, 3 were false-negative "failures" that had actually finished) | `py -m pytest operator-scripts/tests/test_greenlight.py -q` (210+) |
| Timeout salvage | timed-out runs report their partial transcript instead of losing everything | same suite, TestTimeoutSalvage |
| git cross-check before failing (gl-0100) | a timeout is not a failure if the work landed in git | greenlight tests |
| Chain runner pattern | keeps a project's queue flowing at BelowNormal during game mode | scratchpad script pattern; rewrite per use |
| Game-session co-existence (gl-0060 VALIDATED) | demoted workers + game-mode dispatch freeze; 5 workers under Deadlock, zero lag | headroom fix / game off |
| Greenlight curator + morning brief | proposes the day; Kruz !oks | 8:10 brief |

## Command Tower (the flagship — HIGH PRIO lane)

State at handoff: M0 + perfection bar + M1a (stack/combat) + M1b (full
commander rules, 6 seats) + M2a (card DSL + harness) + ATLAS (192 precons,
3,153 CR rules) + client skeleton (approved design) + rename done.

Post-Fable operation:
- **M2 wave is self-chaining** (gl-0174 lineage): each batch scripts ~18
  Marvel cards, updates the ledger HONESTLY, re-proposes itself on the
  auto lane; every 5th batch proposes a fidelity audit (decision lane —
  Kruz oks). Sonnet-grade work by design. Monitor via the board; the wave
  ends with an M2-COMPLETE report item.
- **M2b-1/M2b-2** implement the Marvel engine punch-list (Vehicles/Crew,
  Treasure, Sagas, Backgrounds, Monarch, extra combats). Opus-worthy if a
  lane struggles; route with `--model claude-opus-4-8`.
- **M3 builds against docs/M3-ARCHITECTURE.md** (Fable's last design doc):
  M3a server core -> M3b AI seats/undo/reconnect -> M3c relay+deploy ->
  M3d polish. Sonnet implements; do NOT redesign the protocol — deviations
  need a written reason in STATUS.md.
- Hard rails: PRIVATE forever; honest ledger (never soften statuses);
  playtests Kruz-gated; ward-as-tax debt clears in M2b-2 or stays ledgered.
- Known cleanups if not already done by gl-0173: leftover
  `Projects/command-zone/` debris dir (verify-before-delete), stale local
  branches.

## Daily loops (unchanged unless noted)

- 8:10 Greenlight brief -> Kruz !ok -> pump every 30 min; burn flag for
  away windows (knee = 5), game mode zeroes dispatch automatically.
- Clipforge 08:00/08:20, HermesNightlyAudit 02:00, weekly radar Sun 10:00,
  Magpie radar Sun 08:30 — all untouched this week, all fine.
- ARTFORGE: waiting on ONE human step — Kruz's ~10 min Graves freecam
  capture (CAPTURE.md). After capture: assembly (grave_phonk is the locked
  track in catalog.json) -> QC rubric -> #clip-review VETO. Sonnet can run
  assembly; the QC rubric is numeric.
- CK3 Saga Engine: batches self-serve through the validator; playtest
  stage is Kruz-gated. Batch 3+ = escalation stages then ending forks.

## Model routing after Fable

- Default stays Sonnet (greenlight router unchanged). kind=design/
  architecture/verdict already routes Opus.
- Explicitly route Opus for: engine subsystems that touch the rules core
  (M2b lanes if they bounce), M3a protocol implementation review, any
  adversarial audit gate, REM/memory consolidation.
- Haiku for: chores, retries of mechanical sweeps, status refreshes.

## Waits for the next top-model window

1. M3 post-implementation design review (protocol + reconnect edge cases).
2. Real ward / full trigger-system design review (if M2b-2 didn't clear it).
3. Next ARTFORGE template study (a NEW reference edit -> new anatomy).
4. The next mega-review/harvest like this one.
5. Anything Kruz tags "most ambitious" — park the ambition, keep the spec.

## The week, in one line

Two flagships born from two sentences ("housekeeping" -> ARTFORGE;
"I want our own Commander client" -> Command Tower), one infrastructure
overhaul (timeouts/salvage/validation), 125+ verified shipped items — and
the operating doctrine held: propose -> Kruz decides -> verify -> ship.
