# Portfolio Inventory and First-Dollar Report

**Date:** 2026-07-09
**Scope:** `C:\Users\Kruz\Desktop\Projects`, portfolio memory, scheduled
workflows, active revenue lanes, and the current benchmark north star.
**Operating mode:** safe-local only. No outreach, listing, deploy, account,
payment, game launch, public post, or production-data action was taken.

## Executive verdict

The portfolio is not short of finished software. It is short of closure on the
last externally validated step. The strongest move is not another product or
another orchestration layer. It is a one-week revenue-proof push around the
already-built PC Deep-Dive Report, with the RAM sale as a fast cash side path
and Herald held behind its deliverability/send gate.

The largest operational defect was a burn loop: Stoker repeatedly recreated
the same five completed or quota-failed templates. The queue had reached 449
items, and 266 entries were repetitions of those five jobs. That is now fixed
with an explicit provider-quota circuit, terminal cooldowns, weekly/three-day
template cadences, and denial suppression. The first real scheduled tick after
the fix exited cleanly and left the queue at 449.

## Inventory snapshot

The refreshed portfolio-index build reported 138 project roots, 3,326 docs,
and 99 idea/brainstorm files. Its active at-a-glance table currently contains:

- 125 projects and 3,096 indexed docs
- 77 projects with a `STATUS.md`; 48 without one
- 70 projects with no indexed doc since 2026-07-02
- 32 projects with no indexed doc since 2026-06-09
- 101 Git repositories; 25 working trees with changes during the scan
- 104 GitHub Actions workflow files across the tree
- 105 broadly portfolio-related scheduled-task definitions: 77 ready and 28
  disabled

These are not 48 missing-document emergencies or 25 broken repos. Many are
parked projects, nested standalone repos, generated artifacts, or intentional
runtime state. The useful cleanup rule is: require current status only for an
active lane, and inspect each dirty nested repo in its own Git context before
calling it debt.

The core scheduler path is healthy: Greenlight, Stoker, Hermes, portfolio
index, and active Clipforge tasks were ready with successful recent results.
`OperatorDaemonKeeper` is disabled with a missing-path result, but the daemon
was intentionally retired and Greenlight fills that front door. Several other
disabled media/skin tasks are also intentional holds. The Gaming Guard probe
timed out during the audit; a direct process check found no game process. That
timeout is a monitoring-quality issue, not evidence that a game was running.

A limited common-action-version scan found old `actions/checkout@v3` and
`actions/setup-node@v3` references only in large fork/vendor-style repos
(`path-of-building-fork` and `deadlock-mod-manager`). They were left alone.

## What needs attention

### P0 - Keep the operator from manufacturing work

**Fixed now.** Stoker previously deduplicated only open work. As soon as a task
finished or failed, the same template could be proposed again on the next pump.
The provider was explicitly returning a weekly-limit error, while Stoker's
local estimate still believed capacity remained.

The corrected behavior is:

- an explicit current-epoch provider weekly-limit response closes the circuit
  until the configured reset;
- successful full-corpus, status, and PL test sweeps cool down for seven days;
- successful calibration and Codex audit passes cool down for three days;
- ordinary failures can retry after 24 hours;
- a Kruz denial suppresses the same template for the billing epoch;
- open-item title/key dedup remains in place.

Proof: 85 Stoker tests pass, the standing-denylist doctrine check passes, a
dry run reports the circuit open until 2026-07-12 00:01, and the scheduled
2026-07-09 13:05 run returned `0x00000000` without growing the queue.

### P1 - Close one earned-revenue loop

**Decisive recommendation: make the $29 PC Deep-Dive Report the first earned
dollar target.** It is server-generated, has a durable private delivery route,
and already has a documented kill switch and test-mode rehearsal. During this
audit it passed 87 tests, TypeScript validation, and a production build of 132
static pages plus the checkout, webhook, status, and report-delivery routes.

The remaining steps are deliberately gated, not engineering unknowns:

1. Provision the `report_orders` Supabase table with RLS enabled and no public
   policies.
2. Create the $29 Lemon Squeezy product in the existing store's test mode.
3. Set the three dormant report flags, redeploy, and rehearse checkout through
   private report render/download/print while still in test mode.
4. Only after that proof, get a separate explicit Kruz yes to leave test mode.

Canonical recipe: `pc-bottleneck-analyzer/specs/DEEPDIVE_GOLIVE.md`.

### P1 - Use the RAM kit for fast cash, not fake product validation

The installed Corsair `CMW32GX4M2C3200C16` 2x16 GB DDR4-3200 kit is the fastest
cash path, but selling an owned asset is cash conversion, not earned product
revenue. The old kit used 40 broad search results polluted by laptop RAM,
different capacities, 4x8 kits, LPX, and new inventory. The generator now
qualifies exact used desktop-kit attributes; 7 of 40 results survived, with a
current median/recommended list near $150.

The updated conservative estimates are about $116.29 net through eBay after a
13.6% + $0.40 assumed non-store fee and $12.90 shipping, or about $149.99 for a
local cash handoff. Search prices move, so re-run the generator immediately
before listing. The physical gate remains: replacement RAM must be ready, then
swap, boot-test, photograph, and explicitly approve the real-money listing.

Current kit: `flipradar/KIT-ram-32gb.md`. Proof: 43 tests pass and the generator
redetected the installed part number before producing the kit.

### P1 - Stop refreshing benchmark evidence that has already converged

The LLM benchmark suite is healthy but artifact-heavy: 173 efficiency run
files, 28 record files, 24 audit reports, 19 routing tables, and 14 TPVS reports.
The latest corpus has 57,237 turns and $11,594.03 modeled cost. Fifty-plus
near-identical census refreshes do not answer the remaining product questions.

The next benchmark work should be only:

- the real 20 human grades already called out in `STATUS.md`;
- replay experiments on recommendation-changed/thin-sample classes;
- a refresh only after a material corpus delta or the new cadence expires;
- a decision about what external user consumes this reference lab.

This audit also corrected 47 status summaries that said residual categories
were under a bound "combined." The latest raw data actually shows 4.62% of
turns / 5.64% of cost combined, while each residual category is individually
at or below 2.5%.

### P2 - Finish Herald D3 before calling it a revenue engine

Herald has the highest ticket ($3,500) and 10 staged Send/Skip candidates, but
it is not yet the nearest dollar. Fresh emails still need MX validation, the
interactive D3 send handler is unbuilt, and no live end-to-end tracked send has
been proved. The personal-network scrub passed, but that does not replace
deliverability proof. Finish D3 locally, then keep every actual send behind a
Kruz tap. No cold contact was made in this audit.

### P2 - Treat source-of-truth drift as a revenue bug

The active context incorrectly named AATOS as the nearest follow-up, while the
canonical repo had been archived after the client declined on 2026-07-02. The
client note and first-dollar memory are corrected, the portfolio index was
rebuilt, and the First Dollar Director skill now requires a recency/repo-state
check before ranking a lead. AATOS should not reappear unless Kruz explicitly
reopens it.

## Fixes shipped in this audit

| Area | Change | Verification |
|---|---|---|
| Operator | Quota circuit, terminal/denial suppression, meaningful backlog cadences | 85 tests; doctrine rail pass; scheduled tick proof |
| Benchmark | Defensive queue/checkpoint ingestion, model-alias retention, O(N) passive stats lookup | 174 full-suite tests pass |
| Benchmark truth | Corrected 47 misleading residual-category summaries | Raw gl-0433 artifact reconciled |
| FlipRadar | Exact RAM comp qualification, current fee/postage model, truthful local cash net | 43 tests; regenerated exact-part kit |
| PC Deep-Dive | Pinned Turbopack root to the app instead of the umbrella Projects lockfile | TypeScript pass; production build pass |
| Revenue memory | Marked AATOS declined/archived and updated First Dollar Director recency rules | Index rebuilt; skill validator pass |
| Portfolio memory | Rebuilt `PROJECT_INDEX.md` and `AGENT_CONTEXT.md` | AATOS correction appears in generated context |

The full `operator-scripts` suite exceeded the two-minute audit wrapper without
returning output. The changed Stoker surface is covered by its passing 85-test
module and the real scheduled-run receipt. The benchmark suite has one existing
Python 3.14 warning from LangChain's Pydantic v1 compatibility layer; it is not
a failing test but should stay on the dependency watch list.

## One-week proof path

1. **Earned dollar:** authorize the agent to execute the PC Deep-Dive test-mode
   recipe. Keep the separate real-money switch closed until the entire test
   purchase and delivery loop is proved.
2. **Fast cash:** only if replacement RAM is already economically justified,
   perform the swap/test/photo gate and approve one local-first listing.
3. **High ticket:** finish Herald D3 validation and handler proof; then Kruz can
   tap a very small first batch.
4. **Benchmark:** spend no more cycles on census refreshes this week. Put the
   next available evaluation block into human grades or changed-class replay.
5. **Portfolio hygiene:** do not mass-create 48 status files. Mark a small
   active set, archive/park honestly, and let the rest stay quiet.

Success is not another dashboard. It is one test-mode product loop proved, one
explicit real-money decision presented, and one real receipt captured without
weakening any gate.

## Current external references

- eBay seller fees (category/account dependent; conservative non-store
  assumption used): <https://www.ebay.com/help/selling-fees/selling/selling-fees?id=4822>
- USPS Notice 123, effective 2026-04-26 (Ground Advantage and flat-rate retail
  ceilings): <https://pe.usps.com/cpim/ftp/manuals/dmm300/Notice123.pdf>
- Facebook local pickup safety guidance:
  <https://www.facebook.com/help/287743092672086/>
