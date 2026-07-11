# GambaTime Next Move: ProofCut

**Decision date:** 2026-07-10  
**Decision:** Build the ProofCut creative layer before adding another product,
catalog surface, or upload lane.

## Executive verdict

GambaTime is the portfolio's strongest distribution asset, but the new
canonical renderer is now the constraint. The first repaired canary is safe and
verified, yet it replaced the channel's proven footage-plus-karaoke format with
a mostly static broadcast card. The next sprint should merge GambaTime's new
verification, identity, preflight, and resumable-publish gates into the existing
real-footage karaoke renderer, then grade the result with `engagedViews` rather
than raw Shorts starts.

This is the best next move because it connects the one project with an audience
to the already-live Game Deals money pipe. It does not create another product
that still needs distribution.

## Evidence that changed the ranking

- GambaTime currently has **45 subscribers, 78,327 channel views, and 227
  videos**.
- A current Analytics API pull over 197 measured videos returned **76,377 raw
  views, 32,164 engaged views, 44 subscribers gained, and one lost**. Only
  **42.11%** of starts became engaged views. Subscriber conversion is **0.1368%
  per engaged view**, not the `0.0593%` figure produced by dividing subscribers
  by raw starts.
- YouTube changed Shorts `views` to count starts/replays with no minimum watch
  time. `engagedViews` retains the prior "watched past the initial seconds"
  meaning and remains the YPP/Shorts-revenue denominator. The current grader
  requests only `views,subscribersGained,subscribersLost`, so its learning signal
  is incomplete.
- Corrected engaged-view conversion by tagged angle: `build` **0.432%**,
  `tier_list` **0.289%**, `news` **0.217%**, `guide` **0.178%**, and `drama`
  **0.039%**. Useful/player-impact formats beat generic drama.
- The repaired Palworld canary produced only **2 meaningful-motion frames in 59
  samples (3.4%)**. Three historical winners produced **53.3%-81.7%**. They use
  game footage and word-timed captions; the canary uses neither.
- A safe local ProofCut of the exact Palworld script, using Pocketpair's exact
  official **Palworld 1.0 Cinematic Trailer** and the existing karaoke renderer,
  produced **47/58 motion frames (81.0%)**, 1080x1920, 29.45 seconds, -15.84
  LUFS, and -1.47 dBTP. Black, freeze, and silence scans were clean. Artifact:
  `C:\Users\Kruz\.operator\data\gambatime_proofcut_v3\clip.mp4`.
- The same proof exposed a script-structure issue: a generic subscription pitch
  interrupts the story about five seconds in, before the player-impact payoff.
  ProofCut should fix the creative sequence as well as the visuals.
- Pocketpair's current July 2 video guidelines broadly permit monetized video
  posts of its games, subject to restrictions. That is a Palworld-specific
  rights basis, not a reusable assumption about every publisher.
- The channel's earlier retention read found hooks holding about **98% through
  the first three seconds**, followed by the real loss at the hook-to-body
  handoff. Its measured prescription was to deliver the concrete payoff in the
  first beat. The new renderer contradicts that prescription by putting the ask
  at the handoff and deliberately holding the payoff until afterward.
- The $29 PC Deep-Dive Report is technically ready but recorded only **two
  pageviews in the last 30 days**. Enabling checkout now would create inventory,
  not demand.
- Herald has the highest ticket, but its live cron has failed since May 31 and
  its lead list is exhausted. The RAM listing can create cash, but it is asset
  conversion rather than repeatable product revenue.

## Weighted decision matrix

Scores are 1-5. The weighted total favors existing distribution (30%), then
revenue proximity (25%), autonomous control (15%), time to proof (15%), and
repeatable compounding (15%).

| Candidate | Distribution | Revenue | Control | Proof speed | Compounding | Weighted |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| GambaTime ProofCut | 5 | 4 | 5 | 5 | 5 | **4.75** |
| Herald repair | 2 | 5 | 2 | 2 | 4 | 3.05 |
| PC Deep-Dive launch | 1 | 3 | 5 | 4 | 5 | 3.00 |
| RAM sale | 3 | 5 | 1 | 4 | 1 | 2.90 |
| Start another project | 1 | 1 | 5 | 2 | 4 | 2.35 |

## The funnel correction

YouTube makes URLs in Shorts descriptions and Shorts comments non-clickable.
The affiliate block remains useful for disclosure and attribution, but it is not
a usable purchase button. The real path is:

`Short -> clickable profile Game Deals link -> Deals Hub -> affiliate store`

or:

`Short -> Related Video -> clickable long-form description -> Deals Hub`

The post-baseline collector currently shows **zero** `youtube-profile` deltas.
Its apparent outbound success is contaminated by verification traffic: the
offer distribution is uniformly four checks per catalog item, while health
probes were not consistently tagged as synthetic. Do not treat the current
"intent gate met" result as buyer proof.

## ProofCut build contract

1. **Correct the grader first.** Persist raw `views` and `engaged_views`; use
   engaged views for subscriber conversion, retention, and creative ranking.
   Keep raw views as the reach metric. Label channel-level totals separately
   from the attributable current-public-video cohort; deleted/private history
   means those scopes do not reconcile exactly and must not be silently mixed.
2. **Reuse the winner renderer.** Feed GambaTime's exact neural word timings into
   `clipforge.production.assemble`, with Kruz-owned footage first and strict
   official-publisher footage second. Select cached footage by premise/title
   relevance, release-stage match, and recency, not the current date hash; the
   first local proof exposed the hash choosing an older Feybreak trailer despite
   the exact 1.0 trailer being available. Keep b-roll audio unmapped. Persist a
   dated `rights_basis` (`owned_capture`, publisher policy, or explicit license)
   with its URL/receipt; attribution alone is not permission, and an unknown
   basis blocks public use.
3. **Keep the verified-news brand.** Preserve verdict, source outlets, premise
   identity, collision-safe edition IDs, Discord veto, resumable upload, public
   receipt, and affiliate disclosure. Generate every spoken or visual source
   label from the canonical approved `sources` bundle after verification and
   fail closed when a rendered outlet name diverges from that bundle.
4. **Protect the first ten seconds.** Run hook directly into a concrete,
   verified player-impact payoff, then supporting detail. Use at most one CTA,
   after value: either a channel-growth ask or a `Game Deals on our profile`
   commerce cue based on story intent. Never interrupt the opening with a pitch.
5. **Add a visual preflight.** Known-game stories must use story-specific safe
   footage, carry source video ID/title/channel plus attribution, and clear at
   least **20% motion frames** at two samples/second with scene threshold
   `0.03`. Gradient/default footage cannot satisfy the live gate.
6. **Generate a contact sheet.** Include six sampled frames, footage provenance,
   motion score, LUFS, and true peak in the Discord veto receipt.
7. **Create an honest commerce handoff.** On affiliate-eligible/player-impact
   stories, make the one post-payoff CTA a restrained `Game Deals on our profile`
   cue. Do not also ask for a subscription, and do not claim a discount or lowest
   price without current storefront evidence.
8. **Repair measurement.** Tag every automated redirect check with a `*-probe`
   source, archive the contaminated baseline, and rebaseline once without
   changing either public profile link or the Deals Hub surface.

## Bounded implementation surface

- `gambatime/draft.py`, `short_render.py`, and `qc.py`: hook-to-payoff structure,
  one post-value CTA, retained word timings, and the proven karaoke assembly.
- `gambatime/run.py` plus `clipforge.production.footage/assemble`: choose the
  most premise-relevant safe visual and persist its source identity.
- `gambatime/preflight.py` and `veto.py`: fail closed on visual/source mismatch;
  expose contact sheet, motion score, provenance, loudness, and peak in review.
- `clipforge.analytics`, `publish/youtube_up.py`, `gambatime/learn.py`, and
  `scripts/pull_conversion.py`: preserve raw starts while making engaged views
  the creative/subscriber conversion denominator.
- `operator-scripts/tools/deals_funnel_report.py` and all redirect probes: give
  probes explicit `*-probe` sources, archive the contaminated baseline, and
  start one clean measurement window.

Acceptance requires focused tests for caption-offset preservation, story-to-
footage selection, source-render parity, silent b-roll, visual-QC failure,
raw-versus-engaged math, and probe exclusion; then the full Clipforge suite,
one local/private render, contact-sheet inspection, and a dry publish preflight.
No public upload is part of the implementation test.

## Proof-run story selector

Stories must first clear confirmation, source, footage, and freshness gates.
Among eligible stories, rank concrete player impact first, then historical
engaged-view conversion, then exact Deals Hub eligibility. Commerce eligibility
is a tie-breaker, never permission to manufacture a story. Use the commerce CTA
only when the exact offer is live; otherwise use a post-payoff growth CTA or no
CTA.

## Seven-day proof

- `GambaTimeDailyProduce` is still scheduled for `2026-07-11 08:45` on the
  current static renderer. Ship ProofCut before that run or temporarily hold the
  producer; do not let another static canary enter the public queue.
- Publish at most **one verified Short per day**. Quality and distinctiveness
  beat another volume wave.
- Favor player-impact news, guides, comparisons, and factual buyer context.
  Do not revive generic reaction/drama templates or unsupported "best build"
  claims.
- Hold the public profile links and Deals Hub unchanged through
  `2026-07-24T04:57:04Z`.
- Let every video age at least 72 hours before grading.
- Grade: raw starts, engaged views, engaged/start rate, average view duration,
  retention curve, subscribers gained/lost, profile entries, hub outbounds, and
  confirmed Instant Gaming commission.
- First success is one genuine `youtube-profile` entry and one genuine outbound
  offer click. Business success is the first network-confirmed commission.

## Remaining Studio gate

Do **not** attach the currently queued Related Video `qXfmFo0ih4I` to Short
`_37uQjQ2xtg`. The target is a generic June recap with 100 views, one like, no
explicit Palworld chapter, and no Deals Hub link. The existing Palworld
alternatives are legacy Shorts and are not automatically safe bridge content.
Leave the field unset until a relevant, source-validated companion with an
honest disclosed path exists. Attaching that eventual target is the remaining
Kruz-only Studio action because the Data API exposes no writable field.

## Current official platform references

- Shorts description/comment links are non-clickable; profile and Related Video
  links are clickable: <https://support.google.com/youtube/answer/13748639>
- Related Video setup and advanced-feature requirement:
  <https://support.google.com/youtube/answer/14075157>
- `engagedViews` API definition and current Shorts view-count behavior:
  <https://developers.google.com/youtube/analytics/metrics>
- YouTube's inauthentic-content policy explicitly flags mass-produced generic
  templates and rewards materially varied, original content:
  <https://support.google.com/youtube/answer/1311392>
- YouTube says attribution alone does not establish fair use and that copyright
  remains separate from reused-content eligibility:
  <https://support.google.com/youtube/answer/9783148>
- Pocketpair's current gameplay/video policy permits monetized posts subject to
  its restrictions: <https://www.pocketpair.jp/en/guidelines-video-en/>

## Decisive next command

Build ProofCut, run the full Clipforge suite, render one private/local matched
proof, inspect its contact sheet, and only then arm the next public canary.
