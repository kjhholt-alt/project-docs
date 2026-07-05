# Opportunity Map — Personal-Money Lane + Fleet Infra + Rome

_Read-only research feeding a 3-month impact-prioritized roadmap. Generated 2026-06-30._
_Cluster: wishlist-tracker · ledger · treasury · flipradar (personal-money lane) · operator-scripts + hermes (fleet infra) · rome (the new cockpit)._

**Rails that apply across every item below (do not violate):**
- Money projects (ledger, treasury, wishlist, flipradar) are **recommend-only** — never touch live prod, never place trades/buys/lists autonomously, never move money. Kruz clicks "approve."
- **Kruz never merges** GitHub — PRs land as Discord ✅/❌ gates.
- Personal automation = **right-size risk, don't fear-monger.** These are his own accounts/his own money — build it, don't over-guard it.
- `feedback_user_doesnt_run_commands` — never ask Kruz to run SQL/CLI; Claude runs, Discord reports.

---

## 1. wishlist-tracker

**1-line state:** Feature-complete v1 — dashboard, CRUD, auto price-tracking (URL paste → JSON-LD/OG scrape), price history/sparklines, and full bidirectional Discord (`#wishlist` outbound alerts + inbound quick-add poller) all verified end-to-end; only polish items remain.

**Next-work list (impact-ranked):**

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | **Fix the RLS gap**: drop `anon`-granted `USING(true)` ALL policies on `wishlist.items`/`price_history`, move to service-role-only writes | Flagged 2026-06-29, not yet fixed. Same *shape* as the ArmyofClaws exposure (anon CRUD on `USING(true)`), even though blast radius is low (single-user, non-PII). Cheapest possible fix, closes a real advisor-flagged finding before it's forgotten | S | No — mechanical, no new user-facing behavior |
| 2 | **Inline edit-in-place** for an existing item (rename, change target price) | Only real CRUD gap left; today you can add/delete but not adjust a live item without deleting and re-adding, which loses price history | S | No |
| 3 | **PC-upgrade prioritization (bottleneck-aware)** — tie into `pc-bottleneck-analyzer`'s scan data so wishlist items tagged as PC components rank by actual bottleneck impact, not just price | This is the differentiator vs. a plain price tracker — the whole "what to prioritize when I upgrade" promise from the original spec, still unbuilt (Phase 5) | M | No |
| 4 | **Amazon/Best Buy price-fetch fallback** — these retailers block server-side fetch; today it silently falls back to manual entry | Real gap for the highest-traffic retailers (Amazon especially); could try a headless-Chrome fetch (flipradar already proved the "real Chrome channel, not bundled Chromium" trick against FB's bot detection — same lever likely works here) | M | No |
| 5 | **Budget optimizer / shadcn+mobile+animation polish** (Phase 5 catch-all) | Cosmetic, lowest urgency — app already works and is used daily via Discord quick-add | M | No |

---

## 2. ledger (personal CFO / Plaid)

**1-line state:** Contract/scaffold only — `AGENTS.md`+`STATUS.md` exist, repo is created, but **zero app code, DB, or Plaid wiring exists yet**; framed by Kruz as his "biggest build ever," with a real deadline (house closing ~July 2026).

**Next-work list (impact-ranked):**

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | **Phase 1 foundation build**: Next.js scaffold (lift from wishlist-tracker), Supabase schema (service-role-only, anon denied — already decided, not the wishlist shape), Plaid **Sandbox** wired end-to-end fake-bank→real-pipeline | Unblocks everything else; nothing in ledger exists without this. Highest single lever in the whole cluster given the house-closing deadline | L | No — building against an already-decided contract |
| 2 | **Plaid account creation + Trial-tier keys** (the one external signup: ~3 min, can be Claude-driven via Gmail MCP for verification) | Hard blocker for Phase 2 (live data). Small effort, but it's an external-account action — best to just do it and report, per `feedback_delegation_make_best_choices` | S | Soft — the actual bank-connect click is Kruz's by design (never automate that one click), but account creation/keys can be self-served |
| 3 | **Phase 2: live data** — Kruz connects real banks (his one click in Plaid's secure window); real transactions/balances/liabilities flowing; net-worth-over-time chart | This is where the tool starts being real. Liabilities product pulls the mortgage balance/rate directly — feeds Phase 3 | M | Yes — the bank-connect click is explicitly Kruz's, by design, never automated |
| 4 | **Phase 3: house verdict** — down payment + new mortgage → real monthly leftover + cash runway after the move, affordability check | **Time-sensitive** — closing next month per memory. This is the actual reason the project exists right now, not a nice-to-have | M | No (compute + present; decision itself is Kruz's, tool just informs) |
| 5 | **Phase 4: it acts** — bill/cash-flow calendar, budget-aware meal planning ("5 dinners to the grocery budget + shopping list"), "cut $X here" / "this subscription's dead" flags | Big value-add once live data exists, but strictly sequenced after Phases 1-3 | L | No — recommend-only, no autonomous cancellations/payments ever |
| 6 | **Phase 5: always-on** — `#ledger` Discord daily snapshot + alerts (bill-due/low-balance/big-charge) + inbound quick-ask poller, shadcn/mobile/animation polish | Matches the wishlist/treasury pattern already proven twice in this cluster — low-risk to build once data exists | M | No |

---

## 3. treasury (money brain)

**1-line state:** **v0.1 shipped and live-verified 2026-06-30** — reads FlipRadar + Wishlist + rig-scan + eBay comps, produces 4 grounded decisions (TARGET HIT / BUY THIS FLIP / UPGRADE NEXT / ALL-IN PICTURE), posts to `#money` twice daily (10am/7pm), 18 tests passing, grounding audit PASS.

**Next-work list (impact-ranked):**

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | **Auto-comp every rig part a wishlisted upgrade would replace** (not just the RTX 3090) | Named as the explicit "next" in STATUS.md. Right now only one resale number is grounded; every other potential upgrade path (SSD, RAM, etc.) shows as an ungrounded gap rather than a real number. Directly increases the accuracy/coverage of the flagship ALL-IN PICTURE decision | M | No |
| 2 | **Per-deal dedup / posted-state store for `#money`** | Today the digest re-states the whole standing-BUY board every run — noisy over weeks. A small "new since last post" filter makes the channel actually skimmable long-term | S | No |
| 3 | **Tighten the eBay comp query strings** (e.g. the 3090 query is broad: "RTX 3090 graphics card", median across n=50) | Sharper resale numbers = better UPGRADE NEXT accuracy; the strict-comp-matcher already proved its worth catching a real 3090-vs-4080 mismatch bug, so tightening the query is the natural next precision pass | S | No |
| 4 | **Once ledger goes live, add ledger as a 4th stream** (real cash-on-hand, not just flip/wishlist/rig) | Turns "all-in picture" from a hobby-budget view into a true whole-money picture — but explicitly blocked on ledger Phase 2 landing first | M | No (blocked on ledger, not Kruz) |
| 5 | **Rich Discord embeds** (currently plain text by design — reliable, ASCII-safe) | Explicitly deprioritized in STATUS.md as "not necessary" — lowest-impact item in this project | S | No |

---

## 4. flipradar (FB→eBay flip radar)

**1-line state:** Working, scheduled, personal-only acquisitions radar — full pipeline (scraper → prep/identify → eBay comps → score → Discord `#flipradar`) proven live twice daily via `flipradar-scan` (9am/6pm cron), 1,630 offers in `offers.db`, but it is an **operational island**: not registered in operator-scripts' `config/projects.json` or `monitored_tasks.json`, so nobody pages if it silently dies.

**Next-work list (impact-ranked):**

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | **Register FlipRadar into the operator health fabric** (`operator-scripts/config/projects.json` + `monitored_tasks.json`) | This is a named, explicit finding from the 2026-06-28 fleet audit ("FlipRadar should register into THIS fabric, not be an island"). Confirmed still true — 0 hits in either config file today. It's the single most concrete, already-identified gap in this entire cluster: a real working revenue-adjacent tool with **zero watchdog coverage** | S | No — pure plumbing, matches existing pattern exactly |
| 2 | **Add a freshness/staleness window to `offers.db`** | Known v1 gap: offers accumulate across runs with no expiry, so sold/stale listings linger and can pollute scoring/treasury's flip-income number over time | S | No |
| 3 | **Tighten eBay comp queries for RAM** (known v1 gap, "loose for RAM" per memory) | Direct data-quality fix — a loose comp query on a whole category means every RAM-related BUY/WATCH verdict is less trustworthy than the rest of the pipeline | S | No |
| 4 | **Session-death self-heal / alert** — FB session lives in `flipradar/fb-profile`; if it dies, `run.py` presumably fails silently until someone notices no pings | Combined with item #1 (fleet registration), this closes the loop: registration detects "task didn't run/errored," this detects "task ran but got zero listings because logged out" — the more insidious silent-failure mode | S–M | No |
| 5 | **Reconcile the older operator-suite `flipradar/` scaffold** (docs/config/schema stubs) with the real working `Projects/flipradar/` scraper | Memory flags this explicitly as unreconciled ("separate from the working Projects/flipradar scraper above; reconcile later"). Mostly hygiene/confusion-prevention, not urgent | S | No |
| 6 | **Confirm `DECISIONS.md` (D1 discovery / D2 categories / D5 cadence)** | Still awaiting Kruz's sign-off per memory — worth a quick explicit confirm-or-adjust pass since the working pipeline has already outpaced the doc | S | **Yes** — needs Kruz's read/confirm |

---

## 5. operator-scripts + hermes (the fleet infra)

**1-line state:** Two mature, extensively-tested systems (hermes: 792 tests, autonomous PR review/fix/merge-gate broker; operator-scripts: 45+ scripts, `py ops` unified CLI, 3 watchdogs, 4 dashboards) that together ARE the always-on control plane — but a resident daemon (`OperatorDaemonKeeper`) has been dead/disabled since 4/22 (cron currently papers over the gap), and 4 money-lane projects above aren't plugged into the health fabric it provides.

**Next-work list (impact-ranked):**

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | **Decide revive-vs-formally-retire `OperatorDaemonKeeper`** | Named directly in the 2026-06-28 fleet audit as a real (if currently low-impact) gap — cron (ops-status-json/5min, watchdog/30min, taskwatch, digest/8am) covers its work today, but an undecided dead daemon is exactly the kind of stale-trust risk this whole cluster is built to avoid. Cheap to formally retire (update docs, remove dead references) if reviving isn't worth it | S (retire) / M (revive) | Decision itself benefits from a quick Kruz confirm, but the audit/writeup can be done autonomously first |
| 2 | **Fix the `deploy-radar.py:169` InspectFailed blindspot** | Real, already-diagnosed bug: `vercel inspect` exits 1 BY DESIGN on Errored deploys (prints `status ● Error`), but `InspectFailed` isn't in `ALERT_INTO={Error,Canceled}` — so it **never paged for 42 days**, affecting monitoring across ALL 16 projects, not just the one it was caught on (deal-brain). This is a monitoring-coverage bug in the exact system meant to catch failures | S | No — parse-before-bail fix, doesn't touch what gets built, only what gets reported |
| 3 | **Register the 4 money-lane projects (flipradar/treasury/ledger/wishlist) into `operator-scripts/config/projects.json`** | Confirmed via direct grep: 0 hits for any of the four in `projects.json` today. Rome's own DATA_UNIVERSE doc calls out `~/.operator/ops-status.json` as the "single cheapest read for whole-portfolio health" — but it can't see what isn't registered. This is the connective tissue between items already flagged separately per-project above (esp. flipradar #1) — doing it once here covers all four | S | No |
| 4 | **Studio watchdog false-positive fix**: `hud_health_hourly` staleness check assumes an hourly heartbeat that doesn't exist (only writes when the :7777 HUD page is open) | Diagnosed but not fixed as of the 2026-06-28 audit — a real daemon (28h+ uptime) reads as "stale" today, training Kruz to distrust a legitimate green signal (the exact anti-pattern this whole infra exists to prevent) | S | No |
| 5 | **Clean up `ClipforgeSlopRenderOnce` erroring 17d** (hygiene item from the same audit) | Long-standing red that's been ignored; low urgency but it's noise in the health view that could be masking a real future signal | S | No |

---

## 6. rome (the new cockpit)

**1-line state:** Post-MVP integration complete and **verified against real live data** (fleet=9 sessions, ops=4 issues, hero correctly read "NEEDS YOU" amber) as of 2026-07-01 — backend composes 6 modules (state/fleet/cost/health/needs/codex) into one snapshot, frontend surfaces all of it on one row; **only packaging (the always-on second-monitor shell) remains genuinely unwired**, despite the code for it already existing in the repo.

Per the "wire next" 12-item list in `docs/DATA_UNIVERSE.md`, cross-checked against `backend/` module list and `STATUS.md`:

**Already wired (do not re-propose):** #1 fleet (`fleet.py`), #2 ops-status hero (`health.py`), #3 needs-you (`needs.py`), #4 live-tail all transcripts (`tail.py`/`reader.py`), #5 cost/PR (`cost.py`), #7 Codex fleet (`codex_reader.py`, confirmed imported in `reader.py`).

**Next-work list (impact-ranked, covering what's genuinely left):**

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | **Finish + verify the always-on install** (Tauri shell `always_on_top` + second-monitor placement, `pythonw.exe` backend as an at-logon Scheduled Task via `run_hidden.vbs`) | This is the entire point of Rome per its own north star ("type one messy goal → walk away → return to *verified* progress") — a cockpit that isn't actually always on-screen doesn't deliver that promise. `scripts/`+`shell/` code is already committed (7ba8815: launcher `.ps1`/`.bat`, scheduled-task JSON, Tauri `src-tauri/` scaffold) but STATUS.md explicitly flags it as "genuine fork left for Kruz" — nobody has wired/tested it running for real yet. Highest-impact single item in the whole cluster: it's the last mile on a system that's otherwise fully built and proven | M | Borderline — PACKAGING.md's own checklist is all Claude-executable steps (register scheduled task, build Tauri exe, drop in Startup); a first live-running verification is the natural next move, not a decision Kruz needs to make first |
| 2 | **`task-scheduler-watch.db` + `deploy-radar.db` → fleet/ALERT (data-universe item #6)** | Two `SELECT`s = deduped task-health + Vercel-deploy grids, already computed by existing watchdogs (reuse, not rebuild). Directly strengthens the hero's ALERT accuracy using data that's already fresh on disk | S | No |
| 3 | **Cross-repo git loop over `projects.json` allowlist (data-universe item #8)** | Fleet freshness + unpushed-work detection across ~91 nested repos; `/sweep` skill already implements this logic — reuse, don't reinvent. Natural pairing with item #1 above (registering flipradar/treasury/ledger into that same `projects.json` first makes this loop cover the whole personal-money lane too) | S–M | No |
| 4 | **`goals_1.sqlite` (Codex) → NEEDS-YOU (data-universe item #9)** | Surfaces active/`budget_limited` Codex autonomous runs as needs-you items — small, additive, rounds out the Codex-side parity with the Claude side | S | No |
| 5 | **`gh-pr-status-cache.json` → PR failures = ALERT (data-universe item #10)** | A `failed` check is a genuine fire worth surfacing; caveat is the cache's own refresh cadence (can be stale) — pair with a freshness-veto read per Rome's own doctrine | S | No |
| 6 | **Wire `standing_broken_known` (currently hardcoded `False`)** | Would need a small static/semi-static `{project, since, note}` list before a real standing wound (like a multi-day-broken build — see deal-brain's frozen deploy above) can surface distinctly from a fresh ALERT. Directly related to fleet infra item #2 (the deploy-radar blindspot) — fixing that bug first would give this a real data source to read from | S (after fleet #2 lands) | No |
| 7 | **`logs_2.sqlite` (Codex firehose) → LIVE, Codex (data-universe item #11)** | Highest raw signal but heaviest (164 MB) — only attempt after the `ORDER BY id DESC LIMIT N` tail pattern is proven elsewhere; lower priority than the structured sources above | M | No |
| 8 | **`stats-cache.json` + Codex `automations/` + `history.jsonl` → RHYTHM (data-universe item #12)** | Daily sparkline / scheduled-cadence / intent-history — explicitly labeled "non-live, nice-to-have" in Rome's own doc | S | No |
| 9 | **The Codex "passes standalone, fails under full pytest" mystery** | STATUS.md documents an extensive but ultimately inconclusive investigation (couldn't reproduce after 5 repeated full-suite runs); flagged as likely transient/environmental. Worth a background watch (does it recur?) rather than active work right now | — (monitor only) | No |

---

## Summary — item counts per project

| Project | Next-work items | State |
|---|---|---|
| wishlist-tracker | 5 | Feature-complete v1, polish remains |
| ledger | 6 | Contract only, zero code yet — time-sensitive (house closing) |
| treasury | 5 | v0.1 shipped + live-verified |
| flipradar | 6 | Working pipeline, operationally isolated |
| operator-scripts + hermes | 5 | Mature infra, real known bugs |
| rome | 9 | Post-MVP integrated, packaging is the last mile |
| **Total** | **36** | |

## The single highest-impact item in this cluster

**Finish and verify Rome's always-on packaging** (rome #1: Tauri shell + `pythonw` scheduled-task backend). Every other data source Rome could show is already wired and proven live against real data — fleet, health, needs, cost, Codex all compose into one honest, freshness-veto'd snapshot today. The only thing standing between "a fully-built cockpit" and "the actual always-on second-monitor daily driver Kruz asked for" is code that's already sitting committed in the repo (`scripts/`, `shell/src-tauri/`) but has never been run as the real thing. It is Medium effort, not Kruz-gated (PACKAGING.md's checklist is entirely Claude-executable steps), and it's the last mile on a system that's otherwise done — the highest ratio of "close to finish line" to "actually finishes the north star" of anything in this report.

Close second: **register flipradar/treasury/ledger/wishlist into `operator-scripts/config/projects.json`** — a small (S-effort) plumbing fix that simultaneously resolves flipradar's named "operational island" gap, strengthens Rome's cross-repo fleet view, and sets up every future money-lane project to inherit fleet-wide health monitoring for free.
