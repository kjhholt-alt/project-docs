# GambaTime Continuous Improvement Mode

**Operator decision:** Keep video production and publishing running at maximum
practical throughput. Never pause the proven lane merely to wait for an
experiment to age.

## Operating model

GambaTime runs two lanes at the same time:

1. **Throughput lane:** the current verified producer, publish pump, grader,
   fleet, and health monitor remain enabled.
2. **Quality-ratchet lane:** each improvement is shadow-rendered, checked, and
   promoted into a tagged percentage of eligible new videos without stopping
   the throughput lane.

Only an edition-level integrity failure blocks an edition: unsupported facts,
source/render mismatch, unknown rights, broken media, failed audio/visual QC, or
duplicate publication. Weak experiment performance rolls back that treatment;
it does not stop the production system.

## Rolling rollout

| Stage | Treatment share | Promotion condition |
| --- | ---: | --- |
| Shadow | 0% public | Local render, manifest, contact sheet, and QC pass |
| Canary | 25% of eligible stories | No integrity failures and clean experiment receipt |
| Expansion | 50% | Aged treatment cohort improves the primary quality metrics |
| Default | 100% | Improvement persists across at least ten eligible releases |

The system continues producing while videos age. Grade every video at 72 hours
and seven days, but never treat those checkpoints as a release interval.

## Narration without a bottleneck

- Default: one consistent, rights-safe synthetic production voice.
- Optional: human narration as a tagged hero variant when a take exists.
- Never clone or imitate another person.
- Raw ASR supplies timing only. Captions always preserve the approved canonical
  script after number, date, and proper-name alignment passes.

## Improvement queue

Apply one independently tagged creative change at a time where practical:

1. claim-matched evidence inserts;
2. canonical narration/caption alignment;
3. consequence-first topic scoring;
4. hook-to-payoff script ordering with no early generic CTA;
5. story-relevant owned or rights-cleared motion selection;
6. voice pacing and prosody;
7. evidence/caption collision checks at phone scale;
8. raw-start versus engaged-view cohort grading;
9. profile and Deals Hub attribution with probe traffic excluded.

Experiment IDs must be collision-safe and immutable. Every receipt records the
renderer version, narration mode, story angle, topic/game, evidence-cue count,
script hash, footage hash, rights basis, and publish ID. A later grade joins by
that ID rather than by title or date.

## Speed metrics

Track workflow speed separately from viewer quality:

- eligible stories discovered per run;
- verified stories per run;
- renders passing preflight per hour;
- time from source discovery to armed edition;
- queue age and publish latency;
- failed-edition rate and retry recovery;
- videos published per active day.

Quality metrics remain engaged/raw starts, average view duration, retention,
likes/raw views, subscribers gained, profile entries, Deals Hub outbounds, and
confirmed commissions. Throughput is never called quality, and quality analysis
is never used as a reason to idle the engine.

## Current task truth

As verified on 2026-07-11, `GambaTimeDailyProduce`, `GambaTimePublishPump`,
`GambaTimeGrade`, `ClipforgeFleet`, and `YouTubeSystemHealth` are enabled. This
mode keeps them enabled. Cleanup of old public videos remains a separate,
reviewed action and cannot block new production.

## Immediate build

Add EvidenceCut as a shadow renderer behind the running producer, using the
existing consistent voice first. Promote it to 25% only after its local manifest
and QC receipt pass. Continue current production throughout the build and while
the first treatment cohort ages.

## High-frequency Shorts deployment

Shipped and activated on 2026-07-11:

- `GambaTimeShortsProduce` runs at 03:15, 07:15, 11:15, 15:15, 19:15, and
  23:15 local time.
- `GambaTimeDailyProduce` remains as the 08:45 offset anchor.
- Both launchers call the explicit `auto-short --arm --skip-if-active` path;
  neither automatic path invokes long-form.
- Automatic Shorts use a one-hour Discord veto window. The hourly publisher
  normally clears the active edition before the next four-hour slot.
- No verified story and an already-active edition are healthy skips. Source,
  draft, render, QC, runtime lease, and publish failures remain nonzero and page.
- The publisher cap is eight videos/day, above the six scheduled Short slots and
  below the system's broader revised API-quota capacity.
- `YouTubeSystemHealth` and task-scheduler monitoring both treat the frequent
  producer as canonical.

Deployment commits: operator-suite `b221faa`, `135d783`, and `388635f`;
operator-scripts `c35fdcb` and `9a2a269`.

Live proof:

- Existing Mortal Shell 2 edition cleared the revised rolling policy through
  the normal veto/preflight/publisher path and produced verified public receipt
  `EJQHHtbjBAs`.
- The first manual frequent run completed in 123 seconds: 40 current topics,
  14 ranked candidates, four confirmed stories, judge 100, clean render/QC, and
  Discord receipt. It queued the verified EA College Football 27 paid-progression
  reversal as edition
  `gt_20260711T152402211004_ea-confirms-plan_short_43397a` for the hourly pump.
- Online system health finished with zero issues and recognized the frequent
  task, active edition, and latest public receipt.
- Verification: 378 Clipforge tests and 13 task-watcher tests passed.
