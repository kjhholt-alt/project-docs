# Agentic Megaprojects — 2026 Tracker

Four year-long, agentic-coding-built platforms, greenlit 2026-06-30. This file is the accountability layer — the single place to check progress across all four. Each project also keeps its own `STATUS.md`; this doc just rolls them up.

## Context (read once, don't need to re-litigate)
This was a deliberate scope pivot. The active push going into 2026-06-30 was mood-locked to exactly two lanes — war-table + clipforge (see `project_30d_push_2026-06` in memory). These 4 platforms are explicitly *outside* that push, and structurally close to a pattern the portfolio's own 2026-06-29 pull-audit flagged as the thing that starves here: generic tools built for strangers instead of a real personal player (closest cousins: Onyx/Helm, myriad). That tension was raised and the call was made anyway, eyes open — full ambition, no scope-shrinking. The named risks live in each project's own `ROADMAP.md`; don't repeat the debate, just don't let those specific failure modes sneak in unnoticed.

**What's paused / what's not:** war-table's active development (the mega-loop sprints) is paused while this push is live. clipforge is *not* affected — it runs autonomously on its own fleet (`ClipforgeFleet` scheduled tasks) regardless of session focus; daily veto/OAuth gates are still Kruz's as always.

## The four lanes

| Lane | Directory | Mission | Phase | Status |
|---|---|---|---|---|
| SWE Agent Platform | `swe-agent-platform/` | Autonomous multi-agent coding platform — NL task or GitHub issue → planned, implemented, tested, debugged, submitted, unattended | **Phase 1 DONE (verified live)** | [STATUS.md](../swe-agent-platform/STATUS.md) |
| Research Agent Platform | `research-agent-platform/` | Multi-agent deep research — query/papers → searched, synthesized, cited report with identified gaps | **Phase 1 DONE (verified live)** | [STATUS.md](../research-agent-platform/STATUS.md) |
| Agent OS | `agent-os/` | Self-improving multi-agent OS — persistent memory, dynamic tool discovery, long-horizon collaboration | **Phase 1 DONE** (on operator-suite master, PR #99) | [STATUS.md](../agent-os/STATUS.md) |
| LLM Benchmark Suite | `llm-benchmark-suite/` | Agent-powered LLM eval platform — comprehensive coverage, calibrated judges, statistically rigorous | **Phase 1 DONE (verified live)** | [STATUS.md](../llm-benchmark-suite/STATUS.md) |

**First-night result (2026-06-30 → 2026-07-01): ALL FOUR hit their Phase 1 done-bar in one evening, from empty folders, each verifying live rather than claiming it.**
- **swe-agent-platform** — took a real GitHub issue end-to-end (explore → fix → test → commit → push → PR), opened + merged a real PR, 38 tests. Even self-reported a real bug it left behind (`.pyc` still tracked after adding `.gitignore`).
- **research-agent-platform** — real 10-source cited RAG report, 6/6 live tests (real network + LLM, no mocks). Checked existing tools (Atlas/Vision Desk/Groundtruth) for overlap before writing code.
- **agent-os** — Phase 1 foundational OS, cross-session recall proof; STATUS says "done-bar met — flagged for review, not auto-continuing into Phase 2" (exactly the prototype-then-stop behavior we wanted).
- **llm-benchmark-suite** — GSM8K cross-checked to identical 40% on two different adapters (real proof the scoring is adapter-agnostic), HumanEval via real subprocess execution, on the RTX 3090; 36 tests.

That's a strong first-night signal against the 30-day interest bar — the question at the 2026-07-30 checkpoint won't be "did anything get built," it'll be "which of these does Kruz still want to push."

## Repo homes (NORMALIZE later — currently split)
| Project | Home | Note |
|---|---|---|
| swe-agent-platform | own repo `github.com/kjhholt-alt/swe-agent-platform` | standalone |
| llm-benchmark-suite | own repo `github.com/kjhholt-alt/llm-benchmark-suite` | standalone (rescued 2026-07-01 — see below) |
| research-agent-platform | subdir of `operator-suite` master (PR #100) | lives inside the fleet repo |
| agent-os | subdir of `operator-suite` master (PR #99) | lives inside the fleet repo |

Two are standalone repos, two are operator-suite subdirs. Not wrong, just inconsistent — worth normalizing to one convention (recommend: all standalone, to keep the fleet repo about the fleet). Non-urgent.

## Git cleanup done 2026-07-01
- **llm-benchmark-suite was stranded** — its Phase 1 landed ONLY on the stale `chore/magnum-opus-phase-0-1-docs` branch, never on master. Rescued into its own repo (commit `d834ca3`), so it's safe regardless of that branch's fate.
- **Stale PR #24 closed** ("command_center magnum opus," 2026-05-24 — 123 commits diverged while master moved 302 ahead). Its only recent/valuable content was recovered (research-agent on master via #100; llm-benchmark now standalone).
- **`chore/...` branch deliberately NOT deleted** — it still holds the only copy of some June clipforge auto-commits; kept as a safety net until those are confirmed redundant on master. Do not delete it without that check ([[feedback_verify_before_delete]]).
- **Known remaining mess (non-urgent):** the local `Projects/` checkout is still parked on that stale `chore` branch, so the working tree doesn't reflect master (e.g. agent-os/research-agent real code is on master, not on the parked disk). This does NOT affect the running operator fleet — it runs from a separate `~/.operator/runtime/operator-suite` clone that hard-resets to origin/master each run. Reconciling the local root to master is a careful separate pass (nested repos + ~174 dirty files in the tree), best done deliberately, not rushed.

Each has its own `ROADMAP.md` with 4-5 phases over the 12-month window, and every phase has a concrete "done when" bar — not a vibe, and not "the framework supports X." Three of the four roadmaps explicitly require proof the thing got used on something real before a phase counts as complete; that's intentional and is the actual accountability mechanism, not just a tracking doc.

## Accountability rules
- **Every session in any of these 4 directories updates that project's `STATUS.md` before ending.** No exceptions — this is what keeps it visible instead of going quiet.
- **Weekly check-in**: review this table + all 4 STATUS.md files at least once a week. Any lane untouched for 7+ days gets flagged, not silently skipped.
- **Staleness alerting**: see each project's `STATUS.md` — these are picked up by the same portfolio-wide nightly staleness check that already flags every other project (PROJECT_INDEX.md / PortfolioIndexNightly). No bespoke alerting system needed.
- **Don't let 4 lanes become 4 simultaneous half-finished things.** If they all need active building, pick which one or two get a dedicated `claude -w` session right now; the rest stay scoped-and-waiting in this doc rather than nominally "in progress" with nobody home.

## The real bar (set 2026-06-30, after kickoff)
The 12-month phase plans in each `ROADMAP.md` are sequencing guides, not the success measure. The actual bar: **if Kruz is still genuinely working on, or interested in, a lane 30 days after kickoff, that's a win** — full stop. Doesn't require Phase 4. Doesn't require all 4 surviving. Ideation here was cheap (spare capacity on an external tool did the research), so the real cost is attention, not compute — and attention is what should decide what continues, not a roadmap milestone.

**Checkpoint: 2026-07-30.** Pull all 4 STATUS.md files, see what actually moved vs. what's been sitting at "Pre-Phase 1," and have an honest conversation about which (if any) earned a real ongoing spot next to clipforge and war-table. One survivor is a win. Zero is also useful information, not a failure — it's the same signal the 2026-06-29 pull-audit already runs on every other project in the portfolio, just applied early instead of after a year of sunk cost.

**clipforge stays the standard these get measured against** — 55k views, built from scratch, real automation, real proof of pull. "Runs unattended on its own fleet" means it doesn't need build-time right now, not that it drops out of the conversation — it stays in the rotation of what gets reported on alongside these 4.

**Phase 1 timelines say "Months 1-3" in each ROADMAP.md — treat that as the outer bound inherited from the original year-long research, not the target.** Kruz's actual loop is idea → structured → **prototype to review** → decide. Aim to hit Phase 1's done-bar well inside the 30-day window. The moment a session hits it, **stop, update STATUS.md, and flag it for review — don't auto-continue into Phase 2 unattended.** A prototype sitting unreviewed for weeks while the agent keeps building defeats the point of the checkpoint.

## Origin
Came from a research dump Kruz did externally on 2026-06-30 (4 candidate "big ambitious year-long agentic projects" + a detailed scaffold for the benchmark suite specifically). Scoped into this tracker + 4 project directories the same day.
