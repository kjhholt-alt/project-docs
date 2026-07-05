# Live / Real-Pull Products — 3-Month Opportunity Roadmap Input

_Read-only research report. Generated 2026-06-30. Feeds an impact-prioritized 3-month
work roadmap. No files were changed and no git mutations were made producing this._

**Cluster scope** (per `project_pull_audit_2026-06-29` memory — these are confirmed
REAL-PULL or "strong 4" tier, i.e. Kruz actually plays/uses them, not SaaS-for-strangers):
clipforge/GambaTime, pc-bottleneck-analyzer (benched), groundtruth + games-hub +
poe2-buildforge (the grounded game-intel stack), content-radar + buildkit-brief (the
content flywheel).

**Doctrine held throughout:** player-first (real player > meta-tooling), artifact lane
not SaaS-for-strangers, honest monetization only, no live-prod autonomous changes, no
fear-mongering about personal automation. Every "Kruz-gated" tag below means a real
decision/credential/spend call — not busywork gating.

---

## 1. clipforge / GambaTime Daily

**Current state:** Fully built pipeline (discover → verify → produce → judge/QC →
veto-queue → pump → grade), all 3 scheduled tasks armed and firing on schedule — but
**zero videos have published**, and two live bugs are actively crash-looping today on
top of the known webhook blocker.

The `clipforge/` checkout at Projects root is the whole operator-suite monorepo, parked
on branch `chore/magnum-opus-phase-0-1-docs` (123 ahead / 302 behind `master`). GambaTime
code itself is verified byte-identical between the parked branch and `master` (confirmed:
`git show master:./IDEAS/gambatime/STATUS.md` matches on-disk exactly) — the divergence
risk is real for the portfolio generally but does not undermine anything below. One
narrower stranding: `IDEAS/gambatime/deploy/` (the launcher scripts + `DEPLOY.md`
documenting how the 3 scheduled tasks were registered) exists only on the parked branch,
not `master` — the tasks run fine today via already-copied `~/.operator/` launchers, but
the recipe to re-register them from scratch would be missing if this branch is ever
cleaned up.

**Confirmed live right now** (via `operator-scripts/secrets.py` + direct `.env` length
check, not printing the value): `DISCORD_WEBHOOK_URL` in `operator-scripts/.env` is 30
characters and does not match the `https://discord.com/api/webhooks/...` shape — it is
still a placeholder, exactly as STATUS.md says. This is the documented fail-closed
blocker for both GambaTime and the live `auto_news` lane.

**NEW — discovered live during this research, not in any prior doc:**
1. `pump.py` has crash-looped **every hourly run from 09:00 to 23:00 today** (14
   consecutive failures) on a `UnicodeEncodeError` — an emoji landed in a generated
   `yt_title` and the fail-closed warning log line chokes on Windows' cp1252 console
   before it ever reaches the Discord-post call. Net effect: the fail-closed warning
   message has *never* actually been sent, even though the pump is correctly holding.
   This is the exact `reference_windows_scheduled_task_cp1252_ascii` gotcha from memory,
   but live-breaking rather than silent.
2. Today's only unattended `GambaTimeDailyProduce` firing (its first-ever unattended
   run — the 06-29 edition was hand-authored) **failed outright**: `draft.py`'s
   `claude -p` call returned no parseable JSON on both retries, the exception is
   swallowed with no diagnostic detail, and no new episode was queued for 06-30. The
   queue is currently stuck on yesterday's story.

### Impact-ranked next-work

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | Fix the `pump.py` Unicode crash — encode-safe the title before logging, or set `PYTHONIOENCODING=utf-8` in the 3 launcher scripts | **The actual proximate blocker right now**, ahead of the webhook — even once the webhook is restored, this crashes the pump before it reaches the post step. Reliability, currently blocking. | S | No |
| 2 | Add error visibility to `draft.py`'s `claude -p` failure path (log `returncode`/`stderr`/raw `stdout` on failure instead of a bare `except: return None`) | Today's failure left zero forensic trail — can't tell timeout vs auth vs format miss. Every day this stays unfixed is another unexplained failure. | S | No |
| 3 | Restore a real `DISCORD_WEBHOOK_URL` in `operator-scripts/.env` (and `operator-core/.env`) | **The actual go-live gate** for both GambaTime and the live auto_news lane — nothing publishes without it, by design. Highest-leverage unblock once #1–#2 stop the crash-loop. | S | **Yes** — credential creation/paste |
| 4 | Retry/verify an unattended produce run succeeds end-to-end after #1–#2 land | Growth/freshness — the queue is a day stale on GambaTime's very first unattended day, undermining the "verified daily" premise before it's live. | S | No |
| 5 | Reconcile `deploy/` launcher docs onto `master` | Low urgency (physical `.operator/` copies already work independently of git state) but the re-registration recipe is currently only reachable from a branch that could be cleaned up. | S | No |
| 6 | Wire long-form auto-drafting into the scheduled `auto` path (currently hardcoded short-only: `episode_file = None`) | STATUS.md's own documented v2 item. Doubles unattended output and feeds the Weekly Recap "reason to subscribe" format. | M | No |
| 7 | Verify the mid-clip sub-beat-before-cliff placement is systematized in the auto-produce template, not just present in the one hand-authored edition | Directly targets the portfolio's documented #1 conversion lever (`clipforge_conversion_sprint`) — GambaTime inherits the same flat 0.046% conversion problem found elsewhere, so this is the highest-leverage growth lever once publishing is unblocked. | S (verify) → M (if fix needed) | No |
| 8 | Re-enable Reddit discovery breadth (currently 429-throttled, discover uses Steam+news only) | Modest growth — more source breadth for lead selection; today's Steam+news-only board already produced a reasonable lead. | S–M | Yes, if it requires new Reddit API creds; no if just pacer-cooldown |
| 9 | Filter CDN/image-host URLs out of groundtruth's "outlet" list in the verifier | Cosmetic trust polish on GambaTime's core differentiator ("verified via X, Y, Z"). Already flagged as cosmetic in STATUS.md. | S | No |
| 10 | Investigate an apparent gap in the hourly pump log around 18:00 today | Low-confidence, low-impact unless it's a pattern (e.g. machine sleep interrupting Task Scheduler). Worth a quick look, not confirmed as real. | S (investigation) | No |

---

## 2. pc-bottleneck-analyzer (BENCHED — security-scoped only)

**Current state:** Benched 2026-06-30 for a public-API security finding, but **the bench
decision is not reflected anywhere in the repo** (no commit, no STATUS.md entry) — last
commit is 06-28 feature work. Per the task's framing, growth/content work is explicitly
out of scope here; this section covers only what's needed to safely un-bench plus one
pure-revenue fix.

All prior High/Med findings from the 06-30 audit were re-verified accurate. One
important nuance surfaced: Vercel's Linux serverless runtime almost certainly does **not**
have the `claude` CLI binary that `/api/analyze` depends on (`claudex.js`'s binary
resolver only checks PATH + Windows-specific paths) — so the "drain your Claude sub"
attack is probably not exploitable in production *today*, only locally or if the deploy
setup changes. This does not reduce the fix's priority (the route is still public,
unauthed, and would become live-exploitable the moment the transport changes), but it
does mean the urgency is "close before it can bite," not "actively bleeding."

Also newly confirmed: **zero rate-limiting exists at any layer** — not just a weak
app-level throttle, there is no `middleware.ts`, no platform firewall rule, nothing. And
a Playwright e2e suite (5 files) now exists alongside the vitest suite, but **neither
runs in CI** (only `lint-blog.yml` and `build-scanner.yml` do) — so "zero tests" is now
stale; "zero *enforced* tests" is accurate.

### Impact-ranked next-work

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | Lock down `/api/analyze` — origin/referer check + per-IP rate limit + cap `messages[]` size (`src/app/api/analyze/route.ts:43-77`) | Closes the only real cost-drain/prompt-injection vector on the site. The blocking item for "safe to un-bench," independent of whether the CLI is reachable in prod today. | M | Code fix: no. Re-enabling/redeploying after: **yes** |
| 2 | Rotate + delete `ANTHROPIC_API_KEY` from `.env.local`; check whether it's also set in Vercel env | It's live, plaintext, and unreferenced by code — if it's *also* in Vercel, that's what would make an exploited route cost real money rather than just local CLI quota. | S | **Yes** — key rotation and Vercel env changes |
| 3 | Fix `src/lib/affiliate.ts:11` — swap placeholder Amazon tag `"bottleneck20-20"` for a real registered Associates tag | **Pure free revenue recovery**, near-zero risk, doesn't touch the benched traffic/growth surface. Every affiliate click today earns $0. Already self-flagged as the queued next step in STATUS.md *before* the bench happened. | S | **Yes** — needs Kruz's real Associates account/tag |
| 4 | Fix `/stats` auth to not store the raw `CRON_SECRET` as the literal cookie value (`stats-auth/route.ts:31`) — use a derived/signed token instead | Caps blast radius of a cookie leak; that secret also gates the Discord digest cron. | S | No |
| 5 | Wire the existing Playwright + vitest suites into CI as a required check, replacing "just `next build`" as the only gate | Tests already exist — this is "turn on what's built," not "write from scratch." Prevents the autopilot or future changes from silently breaking the analyzer, affiliate links, or blog rendering. | S–M | No |
| 6 | Add unit coverage for the untested core analyzer modules (`analysis.ts`, `compare.ts`, `fps-estimator.ts`, `percentile.ts`, `gpu-for-game.ts`, `pcpartpicker.ts` parser) | These are the actual product logic; only the tier-list generator has unit tests today. Lower urgency than #5 since e2e already partially exercises this path. | L | No |
| 7 | Add a per-scan key to `GET /api/scan` instead of returning one global "latest scan" to every caller | Low severity (no PII in a hardware scan) but cheap to bundle alongside #1 while touching adjacent API surface. | S | No |
| 8 | Clamp the unbounded `days` param in `src/app/stats/page.tsx:136` to a sane ceiling | Trivial; bundle with #4 since both touch `/stats`. | S | No |

**Explicitly out of scope:** all growth/content/feature work (autopilot slug-dedupe, the
3-post GPU-bottleneck keyword cannibalization, `/compare` cross-component validation) is
deferred until the security items land and Kruz decides whether/when to un-bench.

---

## 3. groundtruth — grounded game-intel CLI

**Current state:** Live and working. A live `groundtruth status` run today shows 16
sources / 4 engines / 11 skills (STATUS.md's "15 sources / 9 skills" is stale by one
commit — yesterday's `poe.stash_value` bridge to the sibling `poe-vault` repo isn't
reflected yet), 89 tests passing / 8 skipped (grew from the documented 78/1), and the
enforced grounding gate (CI + pre-commit + test suite) has real teeth — a forged
ungrounded skill is caught by both the constructor and the audit. The one STALE source
row (`poe1.ggg.char`, stamped `league=Settlers` vs. the live `league=Ancestors`) is the
freshness layer correctly catching a real PoE1 league change, not a defect.

Confirmed dormant: `sts2.solver` and `factorio.solver` are registered as **sources**
(freshness-tracked) but no skill file actually invokes either engine — `groundtruth run`
genuinely cannot compute a StS2 or Factorio result today, only report source freshness.

### Impact-ranked next-work

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | Wire the StS2 solver + Factorio solver into real skills (e.g. `sts2.damage_forecast`) | Closes the biggest "built but dormant" gap in the stack — StS2 already has a live card-catalog + solver next door, just needs a `Skill` wrapper. | M | No |
| 2 | Authenticated PoE2 league-drift detection, reusing the OAuth token already live in poe2-buildforge (`config/ggg_token.json`, confirmed valid today) | PoE2 is currently the only PoE-family source that can't self-detect staleness; the auth plumbing already exists one repo over. | S | No |
| 3 | Register the `reground` scheduled task (intentionally not yet registered — STATUS.md: "cadence not decided") | Without it, staleness is only caught on a manual `status` run; a daily cron makes the "never silently stale" promise real 24/7. | S | **Yes** — cadence is explicitly named as his call |
| 4 | Add a live CS2 patch-version source (currently module-versioned only) | Lowest-severity honest gap in the stack; cosmetic completeness. | S | No |
| 5 | Add Factorio recipe-level drift detection (needs a Lua-adapter snapshot-to-disk step) | Low priority — Factorio is the newest, least-dogfooded game in the index. | M | No |
| 6 | Turn a skill (e.g. `meta.tier_rank` or `poe.survivability_verdict`) into a standing Discord digest via the existing `--discord` flag | This IS the artifact-lane growth lever per doctrine — converts a pull-only CLI into a push habit Kruz actually sees without invoking it. | S | No |

---

## 4. games-hub — "BuildKit Play"

**Current state:** Deployed and live (`vercel ls` confirms Production, Ready, 1 day old)
with 4 real tier-list pages (Deadlock, PoE1, StS2, HOI4) plus an auto-published grounded
news brief from `buildkit-brief` — **but the branded public domain `play.buildkit.store`
is confirmed NOT wired** (`vercel domains inspect buildkit.store` shows every other
BuildKit subdomain mapped except this one). The README's "not wired yet, Kruz's call"
line is verified true right now, not stale documentation.

Only the Deadlock tier list refreshes live (ISR, `revalidate=3600` against
deadlock-api.com); PoE1/StS2/HOI4 are static-committed data with no refresh cadence and
no staleness signal to the reader — a meaningful asymmetry, since groundtruth already
tracks fresh/stale for two of those same underlying sources (StS2 card_db, HOI4
nation_data) but that signal doesn't reach the public page. One local uncommitted diff
in `public/news-feed.json` (664 ins / 583 del) is a pending content-radar snapshot
refresh that just needs a commit.

`buildkit-brief` is confirmed as a genuinely live 4th feeder into games-hub (daily
`BuildKitBriefDaily` task at 08:30 → personal digest to Discord + gated public
auto-publish; weekly `BuildKitBriefWeeklyDigest` Sun 10:00 → stats/design check-in).
First public edition is live: `https://play.buildkit.store/news/2026-06-30-public` (via
the un-wired domain — the page itself resolves once DNS catches up, or is reachable at
the underlying Vercel URL today).

### Impact-ranked next-work

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | Wire the `play.buildkit.store` domain/alias | **Single highest-leverage unblock in this whole cluster item** — the product is fully built and deployed with zero organic/shareable discovery possible until this trivial DNS step happens. All supporting infra (the `buildkit.store` zone, the pattern proven by every other subdomain) already exists. | S | **Yes** — README explicitly names this his call |
| 2 | Commit the pending `public/news-feed.json` refresh | Keeps the deployed Reddit-signal panel from silently drifting stale. | S | No |
| 3 | Give PoE1/StS2/HOI4 tier lists a live refresh path (mirror Deadlock's `gen:tierlist` pattern or a scheduled regen) | Quality/reliability — three of four tier lists will silently go stale the moment a patch drops, with zero staleness signal today (unlike groundtruth). | M | No |
| 4 | Surface groundtruth's fresh/stale provenance stamps on the public tier-list pages for the sources it already tracks (StS2, HOI4) | Doctrine-alignment win — makes the public site as honest about staleness as the CLI already is, using data groundtruth computes today with no new sourcing. | M | No |
| 5 | Cross-link games-hub tier lists from poe2-buildforge / clipforge outputs | It's a built destination with no funnel — nothing currently drives traffic *to* it. | S | No |

---

## 5. poe2-buildforge (Kruz's own PoB2 + Build HQ)

**Current state:** The most mature and highest-velocity item in this entire cluster
(~150 commits in the last 4 weeks vs. ~10–15 each for the other stack members) —
recent PoE1 feature work landed a legendary-unique scanner (auto-flags build-breaking
uniques, e.g. correctly catches "Chains of Command" as bricking a poison-SRS build via
its minion-restriction clause), a standalone HTML defense cockpit with a
**verified** (not estimated) endurance-charge recompute, and a real-PoB-engine
costed upgrade-ladder planner. 813 tests pass + 2 unexpectedly-xpassed (verified live,
twice, for consistency — meaningfully more than the STATUS.md-quoted 490/587/787 figures,
all of which are now stale to varying degrees).

**GGG OAuth is confirmed genuinely live right now**, not stale: a real
`buildforge.ggg advise "Iceiey"` call succeeded today, returning **Lv74** data (every
number in STATUS.md/CLAUDE.md says Lv67/68/72 — docs, not the tool, are behind). The
archetype detector correctly read `es_stacker (CI / low-life)` and correctly advised
against rolling Life — the Iceiey doctrine (`feedback_poe2_iceiey_ci_lowlife`) holds
structurally in live output, confirming this isn't just an asserted rule but an enforced
one. This directly **corrects** a stale STATUS.md note from 06-25 claiming "GGG token
EXPIRED, Kruz-gated" — that note describes a *different*, separate parked web surface,
not the CLI pipeline Kruz actually uses daily. Despite the live pull path working, the
generated artifacts (`reports/Iceiey*.html`, `snapshots/Iceiey/`) haven't regenerated in
3–4 weeks.

Docs conflate three distinct things under "web/": (a) `buildforge-web`, a genuinely live
deployed guide/share-code tool at `buildforge.buildkit.store`; (b) a *different*, frozen
12-wedge report app that also lives in `web/`, gated by `test_go_no_go.py` whose 2
launch-gate tests are currently **unexpectedly XPASSing** — per the project's own
PARKED.md doctrine, an xpass on a frozen gate is meant to be read as a signal worth
checking, not ignored; (c) a possibly-separate `buildkit-guides`/`poe2.buildkit.store`
mapping that wasn't conclusively traced to this repo.

The PoE2 engine's own honest gap (from CLAUDE.md, distinct from all the PoE1 work
above): DPS is a solid **estimate**, not exact — the attack model is correct but
conversion/ailment-scaling/enemy-resist aren't modeled, ~15 uncommon affixes per
character are unparsed, and the spell base-damage table isn't ingested yet. Structural
verdicts (pool, mitigation, evade%, crit, weapon tier, resists) are reliable.

### Impact-ranked next-work

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | Refresh `reports/Iceiey*.html` and `snapshots/Iceiey/` (one command each; the live pull path is proven working) | High-visibility freshness win — the actual shareable artifact is 3–4 weeks stale despite the underlying character data being fully current. | S | No |
| 2 | Make `--json` output pure JSON (or add a `--json-only` flag) instead of concatenating it after the human report on the same stream | Reliability/integration seam — would silently break any future automation piping this output (e.g. wiring into groundtruth or a Discord digest). | S | No |
| 3 | Investigate the 2 unexpected XPASS in `test_go_no_go.py` | Per the project's own doctrine, this is meant to be a signal — either the parked web app is quietly ready to un-park, or the `xfail` marker is stale. Investigation only; the un-park decision itself is separately gated. | S | No (investigation); un-parking itself is Kruz-gated |
| 4 | Sync STATUS.md's character-level and test-count numbers to reality (currently reads Lv67/68/72 and ~490/587/787 tests vs. live-verified Lv74 and 813) | Pure doc-debt — the doc is the fastest orientation path for any future session and is meaningfully behind the tool's actual state. | S | No |
| 5 | Disambiguate the three overlapping "web/" things in PARKED.md/README.md | Docs currently describe different live/frozen surfaces with the same word, which is exactly the kind of ambiguity that causes a future misjudgment of what's actually live. | S | No |
| 6 | Wire poe2-buildforge's already-live OAuth token as groundtruth's PoE2-league-drift source (ties to groundtruth item #2 above) | Closes a flagged gap in a sibling project using infrastructure proven live today. | S–M | No |
| 7 | Close the ~15 unparsed uncommon affixes + spell base-damage table gap incrementally as they're hit in real pulls | Accuracy — refines the "never fabricate" contract; these are already honestly reported as unparsed, never invented, so this is refinement not a correctness bug. | M (ongoing) | No |
| 8 | ROADMAP.md's Phase 3 dogfood checklist (in-game `.build` load proof) for the parked web app | Only matters if item #3 resolves toward reviving that surface; the CLI is already the proven daily driver regardless. | M | **Yes** — ROADMAP marks this "only you can do this" |

**Cross-cutting note (groundtruth / games-hub / poe2-buildforge):** doc staleness is the
single most consistent finding across all three — test counts, source counts, character
levels, and deploy status are each undercounted by anywhere from one commit to several
weeks. None of the underlying tools are actually broken; the docs simply haven't kept
pace with an unusually high commit velocity. groundtruth and poe2-buildforge also
already share real plumbing (the PoE1 PoB engine bridges directly between them) — they
function more like one system with two front doors than separate feeders.

---

## 6. content-radar

**Current state:** Live, running, and ahead of its own STATUS.md. The daily
`RedditRadarDaily` scheduled task (1:00 PM) is real; git log shows shipped work through
06-29 (multi-platform discovery seam adding Steam + gaming-news RSS, PR #9; a
best-effort mirror of the feed into games-hub's `/news`, commit `b4e0c4b`) that postdates
STATUS.md's own 06-24/25 "last updated" stamp — the doc undersells the current feature
set.

**Confirmed right now:** Reddit OAuth is still **not** activated —
`operator-scripts/secrets.py where/find REDDIT_CLIENT_ID` and `REDDIT_CLIENT_SECRET`
both return "not found" across all 98 scanned `.env` files. content-radar remains on the
keyless RSS-only path (score/velocity/comments are all `NULL`; ranking uses placement-
derived `signal_score` only), exactly as memory says. The games-hub mirror is a
best-effort, non-committing local copy step — separate and deliberately gated from
actually publishing the refreshed snapshot to the live site.

### Impact-ranked next-work

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | Activate Reddit OAuth (paste `REDDIT_CLIENT_ID`/`SECRET` per `docs/OAUTH_SETUP.md`, a documented 2-minute app-creation step) | Flips on real `score`/`upvote_ratio`/`num_comments`/`velocity` (the schema/parsers already exist and are tested, just unpopulated), ends RSS rate-limit throttling, and unblocks GambaTime's "Re-enable Reddit discovery breadth" item above (§1, item 8) — this is the single shared unblock across two cluster items. | S | **Yes** — the 2-minute credential step is explicitly his |
| 2 | Scrape `rising`/`new` in addition to `hot`/`top_day` (currently reps run ~1 day old since `top_day` tops out ~24h) | Surfaces genuinely breaking (<6h) items — the whole point of a *daily* discovery feed. Gate on OAuth landing first (avoids more RSS 429s). | S–M | No (once #1 lands) |
| 3 | Close the feedback loop — feed clipforge's bet grades back into subreddit/topic weights | Turns the radar from a static curated list into something that learns what actually converts to winning shorts — directly serves the portfolio's #1 documented problem (flat conversion). | M | No |
| 4 | Commit a fresh `samples/` snapshot (current committed proof is from 06-25, predates the multi-platform seam and channel re-aim) | Low-value polish — samples are historical proof only, `feed/` itself is gitignored runtime output and stays current via the daily task. | S | No |

---

## 7. buildkit-brief

**Current state:** Live and fully automated, exactly as STATUS.md (dated 06-29,
verified accurate against `auto.py`'s actual gate constants: `MIN_CONFIRMED=5`,
`MIN_NOVEL=3`, `MAX_GAP_DAYS=7`, all confirmed by direct grep) claims. Two editions,
one engine: PERSONAL (his worlds + wishlist price drops, auto-delivers to Discord daily
at 08:30) and PUBLIC (confirmed-primary-source-only, no personal data, auto-publishes to
games-hub `/news` only when it clears the grounding + quality + novelty gate — otherwise
held as a draft, never downgrading a previously-live edition). A weekly
`BuildKitBriefWeeklyDigest` (Sun 10:00) reports production stats, a views link (Vercel
Web Analytics), and a design-direction nudge. First public edition is genuinely live.
15 tests pass covering the grounding gate, public-excludes-wishlist, cross-game filters,
version stamps, and all four publish-gate branches (fresh/thin/nothing-new/stale).

The project's own STATUS.md already names its remaining gaps honestly, all under "your
call": live view *numbers* in the Discord digest (today it only links to Vercel
Analytics, which is collecting but not surfaced as a number — needs one Supabase table +
a games-hub beacon), the Resend email edition (newsletter-shaped content exists, send
path unwired), and the daily-vs-weekly cadence for the public edition.

### Impact-ranked next-work

| # | What | Why / Impact | Effort | Kruz-gated? |
|---|---|---|---|---|
| 1 | Wire the live view-count into the weekly Discord digest (one Supabase table + a games-hub beacon) | Turns "views are being collected somewhere" into an actual number Kruz sees without leaving Discord — closes the loop STATUS.md itself flags as the natural next step. | S–M | No (build); the "say the word" framing in STATUS.md suggests he'd want to greenlight it, so treat as **Kruz-gated** in spirit even though technically buildable now |
| 2 | Wire the Resend email edition (content is already newsletter-shaped) | Near-free add per STATUS.md's own note — a second, more durable delivery channel for the personal edition. | S | No |
| 3 | Decide the public cadence (daily vs. weekly-only) | STATUS.md flags this as an open tuning question — "easy to switch... if daily feels like too much." Pure judgment call, zero code cost either way. | S | **Yes** — explicitly a preference decision |
| 4 | Cross-check the `play.buildkit.store` domain-wiring item (games-hub §4, item 1) — buildkit-brief's own publish path is blocked on the same domain gap | Not a buildkit-brief bug, but its published editions are only fully "live" to the public in the branded sense once the domain lands — bundle awareness with the games-hub fix. | — | Same gate as games-hub item 1 |

---

## Summary — per-project item counts

| Project | Next-work items | Kruz-gated items |
|---|---|---|
| clipforge / GambaTime Daily | 10 | 2 (webhook restore; Reddit creds if needed) |
| pc-bottleneck-analyzer (benched) | 8 | 3 (key rotation, affiliate tag, re-enable/deploy decision) |
| groundtruth | 6 | 1 (reground task cadence) |
| games-hub | 5 | 1 (domain wiring) |
| poe2-buildforge | 8 | 1 (parked-web dogfood checklist) |
| content-radar | 4 | 1 (Reddit OAuth creds) |
| buildkit-brief | 4 | 1–2 (cadence decision; view-count build in spirit) |
| **Total** | **45** | **~10** |

## Single highest-impact item in this cluster

**Fix the `pump.py` Unicode crash in GambaTime Daily** (clipforge §1, item 1). It is the
only item found that is (a) live and currently breaking — 14 consecutive hourly failures
today — (b) sitting directly in front of the single highest-value unblock in the whole
cluster (restoring the Discord webhook), and (c) a same-day, no-decision, S-effort code
fix. Every other item in this report is either a build-out, a doc-sync, or requires a
Kruz decision; this one is a pure "the pipe is clogged right in front of the valve"
problem — fixing it doesn't make GambaTime go live by itself (the webhook restore does
that, and that's rightly his call), but leaving it unfixed guarantees the webhook fix
alone won't be enough.

Close second: **wire the `play.buildkit.store` domain** (games-hub §4, item 1) — the
single cheapest, highest-leverage unblock in the entire cluster in pure impact-per-effort
terms (an already-fully-built, already-deployed product with zero public discoverability
until a DNS step happens), but it is explicitly Kruz-gated, so it can't be picked up
autonomously the way the pump fix can.
