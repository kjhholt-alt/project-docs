# 3-Month Work Roadmap — July/August/September 2026

**Synthesized 2026-07-01.** The deliverable to execute from when a powerful new model drops tomorrow. Built from `OPP_MEGAPROJECTS.md` (46 items), `OPP_LIVE_PRODUCTS.md` (45 items), `OPP_PERSONAL_INFRA.md` (36 items) = **127 raw items**, deduplicated and sequenced against `NIGHT_RUN_2026-06-30.md` + `RECENT_WORK_AUDIT_2026-06-30.md`. Read-only synthesis — no code touched, no git mutated producing this doc.

**Doctrine applied throughout:** player-first (Kruz's real usage beats tools-for-strangers) — clipforge, groundtruth/poe2-buildforge/games-hub, Rome, and the money lane are weighted highest; SaaS-for-strangers and meta-cockpits are weighted lowest. Artifact-lane not SaaS. Never touch live prod autonomously (pcbottleneck stays benched). Money projects recommend-only. Actively-broken beats polish. Unblocks-others beats isolated work. Small-high-impact before big-speculative.

---

## DISCORD SUMMARY (post this)

```
📋 3-MONTH ROADMAP — 127 items sequenced, doc: project-docs/ROADMAP_3MONTH_2026-07.md

Headline: the portfolio's real bottleneck isn't building more — it's 9 short
decisions sitting in front of finished work, plus 2 live crash-loops actively
bleeding right now. Fix those first, everything else compounds.

TOP 5 — START HERE:
1. Fix GambaTime's pump.py Unicode crash (IN PROGRESS, parallel) + draft.py
   silent failure — the pipe is clogged right in front of the valve
2. Wire play.buildkit.store domain — fully-built product, zero discoverability
   until a DNS step (Kruz-gated, 5 min)
3. Grade llm-benchmark's 20 calibration samples — unlocks trusting ANY future
   model-choice decision across all 4 megaprojects (Kruz-gated, ~20 min)
4. Finish Rome's always-on packaging — last mile on the cockpit north star,
   code already committed, just needs to actually run
5. Restore GambaTime's Discord webhook — the actual go-live gate for a
   pipeline that's fully built and armed (Kruz-gated, 5 min)

Shape: Month 1 = unclog + unblock (crash fixes, Kruz decisions, Rome wired
live, security closes). Month 2 = compound the unblocks (harder swe-agent
eval, research/agent-os measurement runs, money-lane fleet registration,
groundtruth dormant solvers). Month 3 = real differentiation (Phase 3 work
on whichever megaproject proved itself, ledger goes live pre-house-closing,
cross-cutting model-selection loop closes).

~10 Kruz-gated decisions total, most under 5 minutes each — see doc's
"KRUZ-GATED DECISIONS" section for the full knock-out list.
```

---

## TOP 5 — START HERE

### 1. Fix GambaTime's `pump.py` Unicode crash + `draft.py` silent failure
**[clipforge]** *(marked IN PROGRESS — being fixed in parallel as of this doc)*
**What:** `pump.py` has crash-looped every hourly run 09:00–23:00 on 2026-06-30 (14 consecutive failures) on a `UnicodeEncodeError` — an emoji in a generated `yt_title` chokes the fail-closed warning log line on Windows cp1252 before it reaches Discord. Separately, today's only unattended `GambaTimeDailyProduce` run failed outright: `draft.py`'s `claude -p` call returned no parseable JSON on both retries, swallowed by a bare `except: return None` with zero diagnostic trail.
**Why:** This is the single highest-impact fix in the live-products cluster by the source doc's own verdict — it sits directly in front of the webhook restore (item #5 below), and fixing the webhook alone won't matter if the pump still crashes before it reaches the post call. It's also the only genuinely *broken-right-now* item in this Top 5 that isn't a decision — pure reliability debt.
**Effort:** S (encode-safe the title before logging, or set `PYTHONIOENCODING=utf-8` in the 3 launcher scripts; add real error logging to `draft.py`'s except block).
**Gated:** No — code fix, no decision needed. *(Status: fix is in progress in parallel per task briefing — verify it landed and an unattended produce run succeeds end-to-end before calling this closed.)*

### 2. Wire the `play.buildkit.store` domain
**[games-hub]**
**What:** games-hub ("BuildKit Play") is deployed and live — 4 real tier-list pages plus an auto-published grounded news brief from buildkit-brief — but the branded public domain is confirmed NOT wired (`vercel domains inspect buildkit.store` shows every other BuildKit subdomain mapped except this one). README explicitly names this as Kruz's call.
**Why:** The single cheapest, highest-leverage unblock in the entire live-products cluster in pure impact-per-effort terms. A fully-built, fully-deployed product sits with zero organic/shareable discoverability until one DNS step happens. All supporting infra (the buildkit.store zone, the pattern proven by every other subdomain) already exists — this is pure "flip the switch." It also unblocks buildkit-brief's public edition from being fully "live" in the branded sense.
**Effort:** S.
**Gated:** **Yes** — README explicitly names this his call (domain/DNS decision).

### 3. Grade the 20 staged calibration samples
**[llm-benchmark-suite]**
**What:** Fill in the blank "Human score" line for each of 20 samples (GSM8K + HumanEval outputs) at `results/calibration/grading_sheet.md` — file confirmed present, staged, ready. The proxy calibration run already happened (Spearman 0.86 vs. a stronger model, explicitly labeled a stand-in for Kruz's actual judgment).
**Why:** This is the actual Phase 2 done-bar for llm-benchmark-suite, and — per the cross-cutting note in `OPP_MEGAPROJECTS.md` — it's the literal precondition for the highest-leverage cross-project idea in the whole megaprojects cluster: using llm-benchmark-suite to justify or challenge the hardcoded model choices baked into the other 3 platforms (swe-agent's `claude-opus-4-8`, research-agent's `claude-sonnet-5`, etc.). Nothing downstream of "trust this judge" can happen until this number is real, not proxy.
**Effort:** S for Kruz (20 short gradings, each already has the judge score shown), S to compute correlation once done (harness already built).
**Gated:** **Yes**, explicitly — literally graded by Kruz's own judgment, cannot be delegated.

### 4. Finish and verify Rome's always-on packaging
**[rome]**
**What:** Rome is post-MVP integration complete and verified against real live data (fleet=9 sessions, ops=4 issues, hero correctly reading "NEEDS YOU" amber) — but the always-on second-monitor shell (Tauri `always_on_top` + `pythonw.exe` backend as an at-logon Scheduled Task) has never actually been run as the real thing, despite `scripts/` and `shell/src-tauri/` code already being committed.
**Why:** This is the entire point of Rome per its own north star — "type one messy goal → walk away → return to verified progress." A cockpit that isn't actually on-screen doesn't deliver that promise, and every other data source Rome could show is already wired and proven live. This is the highest-ratio "close to finish line → actually finishes the north star" item across the whole personal-infra cluster, and PACKAGING.md's checklist is entirely Claude-executable steps (register scheduled task, build Tauri exe, drop in Startup) — this is a first-live-run verification, not a decision Kruz needs to make first.
**Effort:** M.
**Gated:** Borderline/No — self-executable; treat as autonomous unless the always-on placement itself needs a look.

### 5. Restore GambaTime's real Discord webhook
**[clipforge]**
**What:** `DISCORD_WEBHOOK_URL` in `operator-scripts/.env` is confirmed still a 30-character placeholder, not a real webhook URL. This is the fail-closed blocker for both GambaTime and the live `auto_news` lane — nothing publishes without it, by design.
**Why:** This is the actual go-live gate for a fully-built pipeline (discover → verify → produce → judge/QC → veto-queue → pump → grade, all 3 scheduled tasks armed and firing) that has published zero videos despite being ready. Highest-leverage unblock in the whole clipforge lane once items #1 and the draft.py fix stop the crash-loop — GambaTime literally cannot go live without this.
**Effort:** S (create/paste a real webhook URL).
**Gated:** **Yes** — credential creation/paste, explicitly his.

---

## MONTH 1 — Unclog and unblock

Theme: kill active crash-loops, knock out every cheap Kruz-gated decision (most take under 5 minutes and each one unblocks a finished-or-nearly-finished system), close the two genuinely live security exposures, get Rome actually running as the always-on cockpit. This month is dominated by S-effort items with outsized unlock value — the portfolio's bottleneck right now is decisions and small fixes sitting in front of built systems, not new building.

### Week 1 — Crash fixes + Kruz-gated decision sweep

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 1 | **[clipforge]** Fix `pump.py` Unicode crash (IN PROGRESS parallel) + `draft.py` silent-failure error logging | Top 5 #1 — actively breaking right now, 14 consecutive failures | S | No |
| 2 | **[games-hub]** Wire `play.buildkit.store` domain | Top 5 #2 — cheapest highest-leverage unblock in live-products | S | **Yes** |
| 3 | **[llm-benchmark-suite]** Grade 20 calibration samples | Top 5 #3 — unlocks trusted model-selection across all 4 megaprojects | S | **Yes** |
| 4 | **[clipforge]** Restore real `DISCORD_WEBHOOK_URL` | Top 5 #5 — actual go-live gate for GambaTime + auto_news | S | **Yes** |
| 5 | **[research-agent-platform]** Confirm/edit rubric + 5 test topics + "beats baseline" threshold (proposal already drafted in STATUS.md) | Sole blocker for Phase 2; run is mechanical once decided | S (decide) | **Yes** |
| 6 | **[agent-os]** Confirm scenario-set size, metric (k, blended vs. raw), pass bar, embedder choice (proposal already drafted) | Sole blocker for Phase 2's own done-bar; explicitly designed to not be self-decided | S (decide) | **Yes** |
| 7 | **[swe-agent-platform]** Approve/pick a harder eval task set (proposal: multi-file refactors, real debug-loop, reviewer-specialty trigger) | Highest-impact single item in the entire megaprojects cluster — only thing standing between "framework built" and thesis proven/falsified | S (decide) | **Yes** |
| 8 | **[pc-bottleneck-analyzer]** Say go/no-go on closing the open API while benched | Cheap to decide now while attention is on it; currently undecided | — | **Yes** |
| 9 | **[content-radar]** Activate Reddit OAuth (2-min documented app-creation step) | Flips on real score/velocity data already coded+tested but unpopulated; unblocks GambaTime's Reddit-breadth item too | S | **Yes** |
| 10 | **[flipradar]** Confirm `DECISIONS.md` (D1 discovery / D2 categories / D5 cadence) | Quick sign-off; working pipeline has outpaced the doc | S | **Yes** |
| 11 | **[buildkit-brief]** Decide public cadence (daily vs. weekly-only) | Pure preference call, zero code cost either way | S | **Yes** |

### Week 2 — Security closes + Rome goes live

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 12 | **[rome]** Finish + verify always-on install (Tauri shell + scheduled-task backend) | Top 5 #4 — last mile on the cockpit north star | M | No |
| 13 | **[pc-bottleneck-analyzer]** Lock down `/api/analyze` — origin/referer check + per-IP rate limit + cap `messages[]` size | Closes the only real cost-drain/prompt-injection vector; blocking item for "safe to un-bench" | M | Fix: No. Redeploy after: **Yes** |
| 14 | **[pc-bottleneck-analyzer]** Rotate + delete `ANTHROPIC_API_KEY` from `.env.local`; check if also in Vercel env | Live, plaintext, unreferenced by code — if also in Vercel, that's what makes an exploit cost real money | S | **Yes** |
| 15 | **[research-agent-platform]** Rotate plaintext `.env` keys (`ANTHROPIC_API_KEY`, `FIRECRAWL_API_KEY`) before repo's next visibility change | Precautionary; standalone repo one toggle away from public | S | Soft — Kruz's normal rotation flow |
| 16 | **[wishlist-tracker]** Fix RLS gap — drop `anon`-granted `USING(true)` ALL policies, move to service-role-only writes | Same shape as the ArmyofClaws exposure; cheap mechanical fix, flagged since 06-29 | S | No |
| 17 | **[swe-agent-platform]** Fix two finalize safety gaps: detect real default branch (not hardcoded `main`); assert `current_branch != default_branch` before push | Self-referential live bug — this repo's own default branch is `master`; must land before item #7's re-run or the measurement gets corrupted | S | No |
| 18 | **[swe-agent-platform]** Make finalize failures loud, not silent (add `finalize_failed` flag + non-zero exit) | Same corruption risk as #17 — swallowed push/PR failure currently looks identical to success | S | No |
| 19 | **[llm-benchmark-suite]** Fix multiple-choice answer extraction (anchor to final stated answer, not first letter) + add unparseable bucket | Can swing reported accuracy by tens of points; sits on BBH/MMLU-Pro which have no live proof-of-life run yet | M | No |

### Week 3 — Build out the decided-upon measurements + fleet registration

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 20 | **[swe-agent-platform]** Close the reviewer's soft-blocking gate (route to END after MAX_REVIEW_BOUNCES instead of proceeding) | Undermines the "reviewer catches what a single agent misses" pitch; land before item #7's re-run | S | No |
| 21 | **[swe-agent-platform]** Add a second deterministic reviewer check (e.g. "new file with zero test coverage") | Direct multiplier on item #7's chance of a real (non-null) result | S–M | No |
| 22 | **[swe-agent-platform]** Run the harder eval task set + measure (item #7's payoff) | The actual thesis test — multi-agent vs. single-agent on tasks a single agent visibly struggles with | M | No (build); decision already made in #7 |
| 23 | **[research-agent-platform]** Run the baseline-vs-debate-loop comparison per confirmed rubric (item #5's payoff) | Closes Phase 2 for research-agent-platform | S (mechanical once decided) | No |
| 24 | **[agent-os]** Build the recall-scenario harness + run per confirmed metric (item #6's payoff) | Closes Phase 2 for agent-os | M | No |
| 25 | **[operator-scripts]** Register flipradar/treasury/ledger/wishlist into `operator-scripts/config/projects.json` + `monitored_tasks.json` | Named fleet-audit finding — a real working revenue-adjacent tool (flipradar) currently has zero watchdog coverage; fixes all 4 at once | S | No |
| 26 | **[operator-scripts]** Fix `deploy-radar.py:169` InspectFailed blindspot (never paged for 42 days across all 16 projects) | Monitoring-coverage bug in the exact system meant to catch failures | S | No |
| 27 | **[pc-bottleneck-analyzer]** Fix placeholder Amazon Associates tag (`bottleneck20-20`) | Pure free revenue recovery — every affiliate click today earns $0 | S | **Yes** — needs real Associates tag |

### Week 4 — Verify + consolidate Month 1

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 28 | **[clipforge]** Verify an unattended GambaTime produce run succeeds end-to-end post-fixes | Confirms items #1/#5/#9 actually unstuck the pipeline before calling it live | S | No |
| 29 | **[games-hub]** Commit pending `public/news-feed.json` refresh | Keeps the deployed Reddit-signal panel from silently drifting stale | S | No |
| 30 | **[flipradar]** Add freshness/staleness window to `offers.db` | Known v1 gap; sold/stale listings pollute scoring and treasury's flip-income number | S | No |
| 31 | **[flipradar]** Tighten eBay comp queries for RAM | Direct data-quality fix on a loose comp query | S | No |
| 32 | **[operator-scripts]** Studio watchdog false-positive fix (`hud_health_hourly` assumes a heartbeat that doesn't exist) | Trains Kruz to distrust a legit green signal — the exact anti-pattern this infra exists to prevent | S | No |
| 33 | **[operator-scripts]** Decide revive-vs-formally-retire `OperatorDaemonKeeper` | Undecided dead daemon = stale-trust risk; cron papers over the gap today | S (retire)/M (revive) | Soft |
| 34 | **[wishlist-tracker]** Inline edit-in-place for existing items | Only real CRUD gap left — today you delete+re-add, losing price history | S | No |
| 35 | **[poe2-buildforge]** Refresh `reports/Iceiey*.html` + `snapshots/Iceiey/` (one command each) | High-visibility freshness win — shareable artifact 3-4 weeks stale despite live pull path working | S | No |
| 36 | **[poe2-buildforge]** Sync STATUS.md's stale character-level and test-count numbers to reality | Pure doc-debt on the fastest-orientation doc for a high-velocity repo | S | No |

---

## MONTH 2 — Compound the unblocks

Theme: the Month 1 decisions and fixes are now landed — this month is about building on top of them: real measurement runs closing Phase 2 across the megaprojects, growth levers on the now-unblocked live products, dormant capability wired live (groundtruth's solvers, ledger's foundation), and the money lane getting real teeth (fleet registration compounding into actual coverage).

### Megaprojects — Phase 2 close-out and cross-cutting

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 37 | **[llm-benchmark-suite]** Surface unsupported-instruction fraction in IFEval + implement missing checkers | Same silent-inflation class as the answer-extraction bug; makes IFEval numbers trustworthy-or-flagged | S (surface)/M-L (checkers) | No |
| 38 | **[llm-benchmark-suite]** Run BBH and IFEval live at least once (post scoring-bug fixes) | Closes the "no live proof" gap the way GSM8K/HumanEval already did for Phase 1 | S | No |
| 39 | **[llm-benchmark-suite]** Add Wilson/bootstrap confidence intervals to `aggregate()` | Explicitly named in Phase 3's done-bar; pulling forward means every run from now on is trustworthy immediately | M | No |
| 40 | **[llm-benchmark-suite]** Add abstention/error bucket to aggregation | A transient API error and a genuine wrong answer currently look identical; matters once this runs unattended | S–M | No |
| 41 | **[research-agent-platform]** Wire the hybrid search that's already built (`search_hybrid`/`search_vector`/`search_fts`) | Stops re-paying Firecrawl + Anthropic costs on repeat topic runs — directly relevant given item #23 re-runs the same topics | M | No |
| 42 | **[research-agent-platform]** Parallelize fetch/summarize (currently fully sequential) | ~10x wall-clock reduction on the most latency-sensitive path; makes iteration on Phase 2 comparisons faster | M | No |
| 43 | **[research-agent-platform]** Add a claim→source verifier node (KB already stores `derived_from` edges) | The explicit differentiator vs. plain RAG — "grounded in principle" → "grounded and verified" | M | No |
| 44 | **[research-agent-platform]** Strengthen `test_pipeline_e2e.py` grounding assertions (`max([n]) <= source_count`, unique URLs) | The one regression test for the project's core trust claim doesn't actually test it | S | No |
| 45 | **[research-agent-platform]** Fix `MIN_SOURCES_TARGET` acting as a hard max (silently caps at 10, no floor guarantee) | Undermines the Phase 1 done-bar's own "10+ sources" guarantee | S | No |
| 46 | **[agent-os]** Add dedupe/summarize pass for tool-call facts before/alongside Phase 2 measurement | A noisy facts table changes what the recall scenarios (item #24) actually measure | M | No |
| 47 | **[agent-os]** SQL-prefilter `retrieve_facts` instead of load-all-then-score-in-Python | Becomes a real latency problem exactly when noise-robustness scenarios flood in dozens of memories | M | No |
| 48 | **[agent-os]** Add missing `user_id` index to base `MemoryStore` (AdvancedMemoryStore already has it) | 2-line fix avoids the base store silently becoming the slow path | S | No |
| 49 | **[swe-agent-platform]** Add `eval/config.toml` capturing `{repeats, win_margin, task_set}` | Harness gets re-run repeatedly over the roadmap; stop re-deriving methodology each time | S | No |
| 50 | **[swe-agent-platform]** Route "no test command found" to a terminal state, not the debugger | Debugger structurally can't fix a missing test runner — wastes turn budget | S | No |
| 51 | **[cross-cutting megaprojects]** Consolidate the shared eval-harness pattern emerging across swe-agent/llm-benchmark/agent-os | 3 of 4 projects are independently solving "run N scenarios, score deterministically, report without editorializing" — worth a look now that 2-3 harnesses exist side by side | M | No |

### Live products — growth levers now that publishing is unblocked

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 52 | **[clipforge]** Verify mid-clip sub-beat-before-cliff placement is systematized in the auto-produce template | The portfolio's documented #1 conversion lever (flat 0.046%) — highest-leverage growth item once GambaTime is actually publishing | S (verify)/M (fix) | No |
| 53 | **[clipforge]** Wire long-form auto-drafting into the scheduled `auto` path (currently hardcoded short-only) | Doubles unattended output; feeds the Weekly Recap subscribe format | M | No |
| 54 | **[content-radar]** Scrape `rising`/`new` in addition to `hot`/`top_day` | Surfaces genuinely breaking (<6h) items — the whole point of a daily discovery feed; gate on OAuth (item #9) landing first | S–M | No |
| 55 | **[content-radar]** Close the feedback loop — feed clipforge's bet grades back into subreddit/topic weights | Turns the radar from static curation into something that learns what converts — serves the #1 documented portfolio problem | M | No |
| 56 | **[games-hub]** Give PoE1/StS2/HOI4 tier lists a live refresh path (mirror Deadlock's `gen:tierlist` pattern) | 3 of 4 tier lists will silently go stale the moment a patch drops, with zero staleness signal today | M | No |
| 57 | **[games-hub]** Surface groundtruth's fresh/stale provenance stamps on public tier-list pages | Doctrine-alignment win — makes the public site as honest about staleness as the CLI already is | M | No |
| 58 | **[games-hub]** Cross-link games-hub tier lists from poe2-buildforge / clipforge outputs | A built destination with no funnel — nothing currently drives traffic to it | S | No |
| 59 | **[buildkit-brief]** Wire the live view-count into the weekly Discord digest (one Supabase table + games-hub beacon) | Turns "views are being collected somewhere" into an actual number Kruz sees without leaving Discord | S–M | No (build); spirit-gated per STATUS.md |
| 60 | **[buildkit-brief]** Wire the Resend email edition (content already newsletter-shaped) | Near-free add — a second, more durable delivery channel for the personal edition | S | No |
| 61 | **[groundtruth]** Wire StS2 solver + Factorio solver into real skills (e.g. `sts2.damage_forecast`) | Closes the biggest "built but dormant" gap — both engines exist next door, just need a Skill wrapper | M | No |
| 62 | **[groundtruth]** Authenticated PoE2 league-drift detection (reuse existing live OAuth token from poe2-buildforge) | PoE2 is the only PoE-family source that can't self-detect staleness; auth plumbing already exists one repo over | S | No |
| 63 | **[groundtruth]** Register the `reground` scheduled task | Cadence was named as Kruz's call (item may have been decided in Month 1 week 1 sweep — confirm) | S | **Yes** — cadence is his call |
| 64 | **[groundtruth]** Turn a skill into a standing Discord digest via existing `--discord` flag | The artifact-lane growth lever per doctrine — converts a pull-only CLI into a push habit | S | No |
| 65 | **[poe2-buildforge]** Make `--json` output pure JSON (or add `--json-only` flag) | Reliability seam — would silently break future automation piping this output | S | No |
| 66 | **[poe2-buildforge]** Investigate the 2 unexpected XPASS in `test_go_no_go.py` | Per project doctrine, an xpass on a frozen gate is a signal worth checking, not ignoring | S | No (investigation); un-parking is gated |
| 67 | **[poe2-buildforge]** Disambiguate the three overlapping "web/" things in PARKED.md/README.md | Docs describe different live/frozen surfaces with the same word — exactly the ambiguity that causes future misjudgment | S | No |
| 68 | **[poe2-buildforge]** Wire poe2-buildforge's live OAuth token as groundtruth's PoE2-league-drift source | Closes a flagged gap using infrastructure already proven live today (ties to item #62) | S–M | No |

### Money lane — build on the fleet registration

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 69 | **[ledger]** Phase 1 foundation build — Next.js scaffold (lift from wishlist-tracker), Supabase schema (service-role-only, anon denied), Plaid Sandbox wired end-to-end | Unblocks everything else in ledger; nothing exists without this. Highest single lever in personal-infra given the house-closing deadline | L | No — building against already-decided contract |
| 70 | **[ledger]** Plaid account creation + Trial-tier keys | Hard blocker for Phase 2 live data; small effort, self-serviceable | S | Soft — account creation self-served, bank-connect click is Kruz's |
| 71 | **[treasury]** Auto-comp every rig part a wishlisted upgrade would replace (not just RTX 3090) | Explicit "next" in STATUS.md — right now only one resale number is grounded; increases accuracy of the flagship ALL-IN PICTURE decision | M | No |
| 72 | **[treasury]** Per-deal dedup / posted-state store for `#money` | Digest currently re-states the whole standing-BUY board every run — noisy over weeks | S | No |
| 73 | **[treasury]** Tighten eBay comp query strings (e.g. broad "RTX 3090 graphics card" query) | Sharper resale numbers = better UPGRADE NEXT accuracy | S | No |
| 74 | **[flipradar]** Session-death self-heal / alert (FB session in `flipradar/fb-profile`) | Closes the loop with item #25's registration — detects "ran but got zero listings because logged out," the more insidious silent-failure mode | S–M | No |
| 75 | **[flipradar]** Reconcile older operator-suite `flipradar/` scaffold with real working `Projects/flipradar/` scraper | Hygiene/confusion-prevention, not urgent but flagged explicitly unreconciled | S | No |
| 76 | **[wishlist-tracker]** PC-upgrade prioritization tying into pc-bottleneck-analyzer's scan data | The differentiator vs. a plain price tracker — the "what to prioritize when I upgrade" promise, still unbuilt | M | No |
| 77 | **[wishlist-tracker]** Amazon/Best Buy price-fetch fallback (headless-Chrome, reuse flipradar's real-Chrome-channel trick) | Real gap for the highest-traffic retailers; today silently falls back to manual entry | M | No |

### Rome — round out the data sources

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 78 | **[rome]** `task-scheduler-watch.db` + `deploy-radar.db` → fleet/ALERT | Two SELECTs = deduped task-health + Vercel-deploy grids, already computed by existing watchdogs; strengthens hero's ALERT accuracy | S | No |
| 79 | **[rome]** Cross-repo git loop over `projects.json` allowlist | Fleet freshness + unpushed-work detection across ~91 nested repos; `/sweep` skill already implements this — reuse. Natural pairing with item #25 | S–M | No |
| 80 | **[rome]** `goals_1.sqlite` (Codex) → NEEDS-YOU | Surfaces active/budget_limited Codex runs as needs-you items; rounds out Codex-side parity | S | No |
| 81 | **[rome]** `gh-pr-status-cache.json` → PR failures = ALERT | A `failed` check is a genuine fire worth surfacing; pair with a freshness-veto read | S | No |

---

## MONTH 3 — Real differentiation

Theme: Phase 3 work begins on whichever megaproject(s) proved themselves in Month 2's measurement runs, ledger goes live with real bank data ahead of the house closing, the money lane gets its 4th real stream, remaining polish and hygiene items get swept up, and the cross-cutting "prove the whole cluster was worth it" moments (megaprojects picking each other's models, swe-agent pointed at real portfolio bugs, llm-benchmark making a real decision) start landing.

### Megaprojects — Phase 3 and cross-cutting payoff

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 82 | **[swe-agent-platform]** Phase 3 — Advanced capabilities (large-codebase understanding, multi-file refactoring, dependency management, security scans) | Where the platform starts doing something a single competent agent genuinely can't — natural differentiation vs. Cursor/Devin/Claude Code itself | XL | No — gated behind Phase 2 (items #7/#22) actually closing first |
| 83 | **[cross-cutting]** Point swe-agent-platform at real bugs already found in the 2026-06-30 portfolio audit (e.g. wishlist-tracker's SSRF/fail-open-auth findings) | A ready-made task backlog of real, verified, file:line-cited bugs — meaningfully harder than synthetic fixtures; doubles as swe-agent's harder-task-set decision | S (select)/M-L (run+verify) | **Yes** — approving a real PR against a real portfolio repo is a trust decision |
| 84 | **[research-agent-platform]** Phase 3 — Advanced synthesis (gap analysis, hypothesis generation, experiment/code-sketch proposals, multimodal support) | Tests the "identify gaps, propose experiments" half of the mission statement against a real bar, not just a template header | XL | No — gated behind Phase 2 (item #23) closing |
| 85 | **[agent-os]** Phase 3 — Multi-agent collaboration + long-horizon tasks (supervisor hierarchies, debate/swarm, checkpointing across restarts) | Adds real persistence across process restarts — the actual "OS" part of Agent OS; current StateGraph has no checkpointer yet | XL | No — gated behind Phase 2 (item #24) closing |
| 86 | **[llm-benchmark-suite]** Phase 3 — Adversarial testing, contamination detection, statistical rigor, parallel execution | Where the suite becomes usable for real model-selection decisions instead of one-benchmark spot checks — direct prerequisite for item #87 | XL | No — gated behind Phase 2 (items #37-40) closing |
| 87 | **[cross-cutting]** Use llm-benchmark-suite to pick the model(s) the other 3 projects actually run | All three currently hardcode model IDs with no measured comparison; a real-decision proof for llm-benchmark's Phase 4 bar AND a legitimate model-selection upgrade for the other three | M once Phase 3 lands | Soft — "representative task slice" is a judgment call |
| 88 | **[llm-benchmark-suite]** Phase 4 — Real-decision proof-of-player checkpoint (used to make a real decision — clipforge's judge agent, Hermes, a megasprint reviewer) | Written by the project itself as a direct cross-cutting mandate; the cleanest "prove the whole cluster was worth it" moment available | L | Choosing which real decision — Kruz's call |
| 89 | **[llm-benchmark-suite]** Gate HumanEval's code execution behind explicit flag + real sandboxing | Currently fine (trusted models only) but the biggest single exposure if ever pointed at an untrusted model provenance | M | Soft — risk-tolerance confirm |
| 90 | **[swe-agent-platform]** Implement `DockerSandbox` behind existing `Sandbox` interface | `SubprocessSandbox` is explicitly not a security boundary — real gap before pointing this at anything unfamiliar | M | No — blocked on Docker install, not a decision |
| 91 | **[agent-os]** Phase 4 — Production + extensibility (observability, security/sandboxing, agent-performance eval suite, plugins) | The "agent-performance eval/benchmark suite" line is a direct explicit hook to llm-benchmark-suite | XL | Soft — "docs a stranger could follow" is Kruz-adjacent judgment |

### Money lane — ledger goes live, treasury gets its 4th stream

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 92 | **[ledger]** Phase 2 — live data (Kruz connects real banks, real transactions/balances/liabilities flowing, net-worth-over-time chart) | Where the tool starts being real — liabilities product pulls mortgage balance/rate directly, feeds Phase 3 | M | **Yes** — bank-connect click explicitly never automated |
| 93 | **[ledger]** Phase 3 — house verdict (down payment + new mortgage → real monthly leftover + cash runway) | Time-sensitive — closing ~July/August 2026. This is the actual reason the project exists right now | M | No — compute + present; decision is his |
| 94 | **[treasury]** Once ledger is live, add ledger as a 4th stream (real cash-on-hand) | Turns "all-in picture" from a hobby-budget view into a true whole-money picture | M | No — blocked on ledger Phase 2, not Kruz |
| 95 | **[ledger]** Phase 4 — it acts (bill/cash-flow calendar, budget-aware meal planning, "cut $X here" flags) | Big value-add once live data exists; strictly sequenced after Phases 1-3 | L | No — recommend-only, no autonomous cancellations ever |
| 96 | **[ledger]** Phase 5 — always-on (`#ledger` Discord daily snapshot + alerts, inbound quick-ask poller, polish) | Matches the wishlist/treasury pattern already proven twice; low-risk once data exists | M | No |

### Live products — remaining polish and completeness

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 97 | **[clipforge]** Reconcile `deploy/` launcher docs onto `master` | Low urgency (physical `.operator/` copies work independently) but re-registration recipe currently unreachable if branch is cleaned up | S | No |
| 98 | **[clipforge]** Re-enable Reddit discovery breadth (currently 429-throttled) | Modest growth — more source breadth for lead selection, now that content-radar's OAuth (item #9) is live | S–M | Yes only if needs new creds beyond content-radar's |
| 99 | **[clipforge]** Filter CDN/image-host URLs out of groundtruth's "outlet" list in the verifier | Cosmetic trust polish on GambaTime's "verified via X, Y, Z" differentiator | S | No |
| 100 | **[clipforge]** Investigate apparent gap in hourly pump log around 18:00 | Low-confidence — worth a quick look, not confirmed as a real pattern | S | No |
| 101 | **[pc-bottleneck-analyzer]** Fix `/stats` auth to not store raw `CRON_SECRET` as literal cookie value | Caps blast radius of a cookie leak; that secret also gates the Discord digest cron | S | No |
| 102 | **[pc-bottleneck-analyzer]** Wire Playwright + vitest suites into CI as required check | Tests already exist — "turn on what's built," not "write from scratch" | S–M | No |
| 103 | **[pc-bottleneck-analyzer]** Add unit coverage for untested core analyzer modules (`analysis.ts`, `compare.ts`, `fps-estimator.ts`, etc.) | The actual product logic; only the tier-list generator has unit tests today | L | No |
| 104 | **[pc-bottleneck-analyzer]** Add per-scan key to `GET /api/scan` instead of one global "latest scan" | Low severity but cheap to bundle alongside item #13's API surface work | S | No |
| 105 | **[pc-bottleneck-analyzer]** Clamp unbounded `days` param in stats page | Trivial; bundle with item #101 | S | No |
| 106 | **[pc-bottleneck-analyzer]** Fix autopilot dedupe (re-publishes same slug ~40+ times) + canonical guard on the 3 near-duplicate GPU-bottleneck posts | Churn + keyword cannibalization on the highest-intent keyword | M | No |
| 107 | **[pc-bottleneck-analyzer]** Fix `/compare/<a>-vs-<b>` rendering nonsensical cross-component matchups | `sameComponent` is already computed and unused — indexable junk pages | S | No |
| 108 | **[groundtruth]** Add live CS2 patch-version source (currently module-versioned only) | Lowest-severity honest gap in the stack; cosmetic completeness | S | No |
| 109 | **[groundtruth]** Add Factorio recipe-level drift detection | Low priority — Factorio is newest, least-dogfooded game in the index | M | No |
| 110 | **[poe2-buildforge]** Close ~15 unparsed uncommon affixes + spell base-damage table gap incrementally | Refinement of the "never fabricate" contract — already honestly reported as unparsed | M (ongoing) | No |
| 111 | **[poe2-buildforge]** ROADMAP.md's Phase 3 dogfood checklist (in-game `.build` load proof) for the parked web app | Only matters if item #66 resolves toward reviving that surface | M | **Yes** — ROADMAP marks "only you can do this" |
| 112 | **[research-agent-platform]** Pin synthesis model to a dated ID (currently unversioned alias) | Cheapest possible fix for a real failure mode — if alias stops resolving, pipeline dies at last step | S | No |
| 113 | **[research-agent-platform]** Fix pytest collection outside `.venv` (add `pythonpath = src`) | Pure friction reduction; matches a known portfolio-wide `py`-launcher gotcha | S | No |
| 114 | **[research-agent-platform]** Restrict SSRF surface in fetch tools (`follow_redirects=True`, no host allowlist) | Low urgency today (URLs come from Firecrawl/arXiv) but cheap to close | S | No |
| 115 | **[content-radar]** Commit a fresh `samples/` snapshot | Low-value polish — historical proof only | S | No |
| 116 | **[buildkit-brief]** Cross-check `play.buildkit.store` domain wiring with games-hub's item | Bundle awareness — buildkit-brief's public path is blocked on the same domain gap | — | Same gate as item #2 |

### Personal infra — final consolidation

| # | Item | Why | Effort | Gated? |
|---|---|---|---|---|
| 117 | **[wishlist-tracker]** Budget optimizer / shadcn+mobile+animation polish (Phase 5 catch-all) | Cosmetic, lowest urgency — app already works and is used daily via Discord quick-add | M | No |
| 118 | **[operator-scripts]** Clean up `ClipforgeSlopRenderOnce` erroring 17d | Long-standing red that's been ignored; low urgency but is noise that could mask a future real signal | S | No |
| 119 | **[rome]** `logs_2.sqlite` (Codex firehose) → LIVE, Codex | Highest raw signal but heaviest (164MB) — only attempt after tail pattern proven elsewhere (item #78/#80) | M | No |
| 120 | **[rome]** `stats-cache.json` + Codex `automations/` + `history.jsonl` → RHYTHM | Daily sparkline / cadence / intent-history — explicitly labeled non-live, nice-to-have | S | No |
| 121 | **[rome]** Wire `standing_broken_known` (currently hardcoded False) | Needs a small static list before a real standing wound can surface distinctly from a fresh ALERT; pairs with item #26's deploy-radar fix | S | No |
| 122 | **[rome]** Monitor the Codex "passes standalone, fails under full pytest" mystery | Inconclusive investigation already done; watch for recurrence rather than active work | — | No |
| 123 | **[llm-benchmark-suite]** Populate remaining `.gitkeep` scaffold directories per mapped ROADMAP phase (analyzers, benchmark_generators, meta_development, robustness/safety benchmarks) | Most of remaining Phase 2/3 breadth — natural session-sized chunks once Phase 2's core measurement (items #37-40) is trustworthy | XL in aggregate | No — breadth items are session-sized, not individually gated |
| 124 | **[llm-benchmark-suite]** Phase 5 — final polish, edge cases, leaderboard, export | Explicitly lowest priority by the roadmap's own sequencing — only after Phase 4's real-decision proof happens | L | No — gated behind Phase 4 |
| 125 | **[swe-agent-platform]** Phase 4 — polish, evals, self-improvement (SWE-bench eval, self-analysis of failure logs, dashboards) | The "real task in your own portfolio" bar dovetails with item #83 | XL | Portfolio-task nomination is Kruz's call |
| 126 | **[research-agent-platform]** Phase 4 — self-improvement + production (curate own sources, eval vs. human baseline, UI/dashboard, exportable reports) | "Another lane in the portfolio" clause is a direct cross-cutting hook | XL | "Real question for another lane" nomination is Kruz's call |
| 127 | **[cross-cutting]** Check Atlas's actual module (not just its schema shape) before any project builds new local-recall infra | research-agent's KB and llm-benchmark's potential future needs both risk reimplementing Atlas's SQLite+FTS5+local-embedding pattern a third time | negligible | No — a checklist reminder, not a build |

---

## KRUZ-GATED DECISIONS (the knock-out list)

Everything waiting on Kruz's call, consolidated. Most of these are under 5 minutes each — doing this whole list in one sitting unblocks a disproportionate share of the roadmap above.

### Quick decisions (read + answer, no external steps)
1. **swe-agent-platform** — approve/pick the harder eval task set (Month 1 #7). Proposal ready.
2. **research-agent-platform** — confirm/edit the quality rubric + 5 test topics + "beats baseline" threshold (Month 1 #5). Proposal fully drafted in STATUS.md.
3. **agent-os** — confirm scenario-set size, metric (k, blended vs. raw), pass bar, embedder choice (Month 1 #6). Proposal fully drafted in STATUS.md.
4. **pc-bottleneck-analyzer** — go/no-go on closing the open API while benched (Month 1 #8).
5. **groundtruth** — decide the `reground` scheduled-task cadence (Month 2 #63).
6. **buildkit-brief** — decide public cadence, daily vs. weekly-only (Month 1 #11).
7. **flipradar** — confirm `DECISIONS.md` (D1 discovery / D2 categories / D5 cadence) (Month 1 #10).
8. **buildkit-brief** — greenlight wiring the live view-count into the weekly digest (Month 2 #59) — buildable now, but STATUS.md's framing suggests a "say the word."
9. **llm-benchmark-suite** — confirm risk tolerance on HumanEval code-exec before Phase 3 adversarial testing (Month 3 #89).
10. **cross-cutting swe-agent** — approve pointing swe-agent-platform at a real portfolio bug (e.g. wishlist-tracker's SSRF finding) for a real, non-fixture PR (Month 3 #83) — a genuine trust decision.
11. **cross-cutting llm-benchmark** — choose which real decision Phase 4's proof-of-player checkpoint targets (clipforge's judge, Hermes, a megasprint reviewer) (Month 3 #88).
12. **poe2-buildforge** — the in-game `.build` load dogfood proof for the parked web app, explicitly "only you can do this" (Month 3 #111).

### Credential / 2-minute unlocks
13. **games-hub** — wire the `play.buildkit.store` domain (Top 5 #2). DNS step, all infra ready.
14. **clipforge** — restore a real `DISCORD_WEBHOOK_URL` (Top 5 #5). GambaTime's actual go-live gate.
15. **content-radar** — activate Reddit OAuth via the documented 2-minute app-creation step (Month 1 #9). Unblocks clipforge's Reddit-breadth item too.
16. **pc-bottleneck-analyzer** — rotate `ANTHROPIC_API_KEY`, check/remove from Vercel env (Month 1 #14).
17. **pc-bottleneck-analyzer** — get a real Amazon Associates tag to replace the placeholder (Month 1 #27). Every affiliate click currently earns $0.
18. **research-agent-platform** — rotate plaintext `.env` keys before next visibility change (Month 1 #15). Soft/precautionary.
19. **ledger** — Plaid account creation + Trial-tier keys, self-serviceable (Month 2 #70).
20. **ledger** — the actual bank-connect click inside Plaid's secure window (Month 3 #92) — by design, never automated, always Kruz's one click.

### 20 real calibration gradings (the one substantive time-cost item)
21. **llm-benchmark-suite** — grade the 20 staged samples at `results/calibration/grading_sheet.md` (Top 5 #3). ~20 minutes of real judgment, unlocks trusted model-selection across the whole megaprojects cluster.

---

## Item count reconciliation

| Source doc | Raw items | Sequenced into Month 1/2/3 |
|---|---|---|
| OPP_MEGAPROJECTS.md | 46 | 46 |
| OPP_LIVE_PRODUCTS.md | 45 | 45 |
| OPP_PERSONAL_INFRA.md | 36 | 36 |
| **Total** | **127** | **127** |

All 127 raw items across the three opportunity maps are sequenced above (items #1-127 across Month 1/2/3, with the Top 5 pulled forward as the entry point). Nothing from `NIGHT_RUN_2026-06-30.md`'s "already done tonight" list — the 4 megaprojects' Phase 2 structural work, the swe-agent/research-agent correctness fixes already landed, Rome's build/wave-2/integration/hardening — is re-listed as todo; those are the foundation this roadmap builds on. The GambaTime `pump.py` fix is marked in-progress (Month 1 item #1) per the parallel fix already underway.
