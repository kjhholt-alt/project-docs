# PC Optimizer Product Angle — structured (gl-0151)

**Origin:** Kruz riff 2, 2026-07-03, during a Deadlock test (`~/notes/00-Inbox/2026-07-03.md`
14:41 entry). Promoted via Greenlight gl-0151, worked 2026-07-06. Sibling note same day:
"headroom-as-product seed" (13:56 entry) — the two riffs are the same insight at two
different altitudes; see the doctrine note below for why that matters.

**The riff, verbatim:** *"beyond headroom-for-us -- a standalone FPS-maximizer/PC-optimizer
product, niche = workstation builders who run heavy dev/AI fleets AND game on one box.
Today's live material: priority inversion discovery, core-pinning limits, disk/AV churn as
the real killer, flow-count ceiling concept, PresentMon elevation gate. Pairs with
pc-bottleneck-analyzer (existing flagship, pcbottleneck.buildkit.store) as the family.
Player-first: prove on Kruz's box first."*

---

## Doctrine flag (once, per feedback_idea_intake_curation)

This riff names its own real player (Kruz's own box) but also names a second, unproven
player: "workstation builders" as a stranger-facing standalone product. Per
`user_player_first_doctrine`, that second framing is the exact shape that starves —
stranger-with-credit-card SaaS, not a thing Kruz or someone he knows uses. Live evidence
already sitting in the portfolio, not hypothetical:

- `pc-bottleneck-analyzer`'s own **Pro ($9.99)** and **Blueprint ($19)** paywalled tiers are
  both fully built, fully wired to Lemon Squeezy, verified in test mode — and **both have sat
  armed-but-never-flipped since 2026-07-01** (see that project's STATUS.md "THE FLIP"
  sections). Nobody has taken the 10-minute step to go live on tools that already exist.
- The portfolio's own 2026-06-29 PULL audit (`project_pull_audit_2026-06-29`) found the
  exact same pattern everywhere: what Kruz personally *plays or runs* gets sustained
  commits; what's built to *sell to strangers* stalls at "built" and never gets pushed
  further, paywall or no paywall.

**This run's call:** treat "prove on Kruz's box first" as the whole scope, not the first
milestone of a bigger build. Everything below proves the *technical* premise (the five
findings are real and now machine-checkable) on Kruz's own box, using headroom — the tool
he already runs daily for game-mode governance. Whether any of this ever becomes a
stranger-facing product on pc-bottleneck-analyzer is a **separate, later, explicit decision**
— not something this run advances or recommends taking on its own.

---

## Technical backbone — what's actually behind each bullet

| Riff bullet | Code | Status before this run | Status after this run |
|---|---|---|---|
| Priority inversion discovery | `headroom/why.py::check_priority_inversion` | Built + tested (real 2026-07-03 Deadlock incident cited in the docstring) | Unchanged — re-verified live today (see Proof) |
| Core-pinning limits | `headroom/governor.py` affinity isolation (`isolate()`/`unisolate()`, perf-cores-free mask) | Built + tested | Unchanged |
| Flow-count ceiling concept | `headroom/capacity.py` ("how many flows can this box carry") | Built + tested (gl-0058) | Unchanged — re-verified live today |
| PresentMon elevation gate | `headroom/flight.py` (elevated-token requirement, `HeadroomFpsOnDemand` scheduled-task workaround) | Built + documented + solved | Unchanged |
| **Disk/AV churn as the real killer** | `state.py`'s `lane_ceiling` docstring: *"however CPU-light, generates disk I/O / Defender scans / scheduler churn that ignores priority class and affinity"* | **Doctrine only** — lived/proven three times, but nothing sampled it directly; not a checkable signal | **NEW: `headroom/diskchurn.py`** — a standalone, read-only sampler + `check_disk_av_churn()` verdict (9 tests), CLI `py -m headroom diskchurn`. Deliberately NOT wired into `flight.py`'s live daemon/schema (that's a production system Kruz's game-mode governance depends on right now; changing its schema was a bigger blast radius than this prototype needed) |

Four of five bullets were already real, tested code — this riff's technical claims aren't
speculative, they're a plain description of what `headroom` already does. The fifth (disk/AV
churn) was the one real gap, closed today.

---

## Proof — run live on Kruz's box, 2026-07-06

```
$ py -m pytest headroom/tests -q
....................................F................................... [ 54%]
............................................................             [100%]
1 failed, 131 passed in 4.86s
```
The one failure (`test_fleet.py::test_launch_normal_mode_spawns_normal_priority`) is
pre-existing and unrelated to this work — the box's live posture is currently `away`
(`py -m headroom posture` → `{"posture": "away", "effective": "away"}`), which the test
doesn't account for when asserting a freshly-launched process gets `NORMAL_PRIORITY_CLASS`.
This run never touched `fleet.py`, `state.py`, or `governor.py`. The new module's own suite:

```
$ py -m pytest headroom/tests/test_diskchurn.py -v
9 passed in 0.76s
```

**The new check, run live, right now** — a real, unprompted hit on the exact mechanism the
riff describes:

```
$ py -m headroom diskchurn --seconds 4
[WARN] DISK/AV CHURN
       MsMpEng.exe (security/AV) used 85% CPU during this window -- disk/AV/scheduler
       churn ignores priority class and affinity (state.py's lane_ceiling=0 doctrine,
       proven live three times), so demoting fleet CPU priority alone does not stop
       this kind of contention.
       evidence: {'elapsed_s': 4.0, 'read_mb_s': 2.1, 'write_mb_s': 2.3,
       'av_procs': [{'name': 'MpDefenderCoreService.exe', 'cpu': 0.0},
       {'name': 'MsMpEng.exe', 'cpu': 84.9}, {'name': 'NisSrv.exe', 'cpu': 0.0}],
       'fleet_procs': 60, 'fleet_cpu_pct': 1.1}
```

Read that evidence line carefully: **60 fleet processes, properly demoted, totaling 1.1%
aggregate CPU** — the existing priority-demotion doctrine is working exactly as designed —
**while Windows Defender alone burns 85% CPU**, completely outside that demotion's reach.
This is the riff's claim, not asserted but measured, in the same minute this doc was
written.

**Corroborating live reads from the existing (pre-built) checks**, same session:

```
$ py -m headroom why --minutes 30
[HOT ] PAGING STUTTER — 35565 hard page faults/s, RAM at 86%
[WARN] NET — 33ms peak / 32ms spread to the gateway
[WARN] CORE CONTENTION — 8 cores pinned >90% while overall CPU averaged 56%
[WARN] THIRD-PARTY STEALER — wmiprvse.exe at 105% CPU
[INFO] FLEET CHURN — 65 fleet processes, 475% aggregate CPU, 6.2GB RAM

$ py -m headroom capacity --days 1
samples 70325 (free 69010, in-game 1315)
BINDING CONSTRAINT: RAM (hot samples cpu/ram/gpu = 641/8884/56)
observed cost per flow ~88MB (median 28.0 fleet proc(s) while free)
CAPACITY: ~61 more flows fit at median load before the RAM ceiling
ZERO-LANES DOCTRINE: VIOLATED -- 0% of 1315 in-game sample(s) at <= 1 fleet proc(s)
```

The capacity read surfaced a real, separate finding worth flagging to whoever owns
`headroom` next: the zero-lanes doctrine is currently being violated during real gameplay
(median 34 fleet procs running while a game has the foreground, not the target of ≤1) — that's
a `project_headroom` governance question, out of scope for this item, not chased further here.

---

## Family relationship to pc-bottleneck-analyzer

The riff calls these "the family" — worth being precise about what that means before anyone
builds toward it:

- **pc-bottleneck-analyzer today** answers a **point-in-time** question: "grade my hardware,
  tell me what to upgrade." One scan, one verdict, no notion of *other processes competing for
  the machine*.
- **This angle** answers a **continuous, multi-process contention** question: "why did my FPS
  dip just now, while I had N other things running." Different axis entirely — closer to
  `headroom`'s `why`/`capacity`/`diskchurn` shape than to a hardware-grading scan.
- If this ever becomes stranger-facing, the cheapest test is a **new mode on the existing
  pcbottleneck site/brand/billing** (e.g., a "Fleet Mode" or "Dev Workstation Mode" tab reusing
  the already-wired Lemon Squeezy plumbing) — not a new standalone product, new domain, or new
  billing integration. But per the doctrine flag above, that's explicitly not this run's
  recommendation to pursue next; it's just the answer to "if," not "when."
- Not touched or audited by this run: pc-bottleneck-analyzer's repo was left alone entirely
  (it's mid-edit on branch `feat/upgrade-blueprint` with other uncommitted work in flight —
  see `project_greenlight_shared_checkout_collision`). Its own tracked security debt
  (`project_pcbottleneck_autopilot_deploys`, the unauthed `/api/analyze` route noted in
  `OPP_LIVE_PRODUCTS.md`) is unrelated and unresolved by this doc.

---

## Scope actually run (hard rails respected)

- No billing, Stripe, or Lemon Squeezy changes. No new product, tier, or paywall built.
- Nothing published publicly; no site, page, or repo made stranger-facing.
- No changes to pc-bottleneck-analyzer's working tree (left untouched to avoid colliding
  with its in-flight uncommitted work).
- The only code change is additive and local to `headroom/` (Kruz's own tool): one new
  module, one new test file, one new CLI verb, a one-line config list, a doc update.

## Suggested next checkpoint (short, real — not a roadmap)

Per `feedback_idea_intake_curation`: idea → structured (this doc) → **prototype to review**
(the `diskchurn` command, already runnable) → Kruz decides. The next real checkpoint is
qualitative, not a milestone plan: **the next time Kruz notices a real FPS dip during a fleet
+ game session, does `py -m headroom diskchurn` (or a `why` run that now includes a DISK/AV
CHURN line, if it gets folded in) actually explain it better than the four existing checks
did alone?** If yes over a session or two, wiring it into the live `flight` daemon/schema is
the natural next step. If it never fires or never adds signal beyond FLEET CHURN/THIRD-PARTY
STEALER, that's a real answer too — leave it as a standalone diagnostic and don't build
further. Either way, the "standalone product for other people" question stays parked until
Kruz raises it, per the doctrine flag above.
