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
   Keep raw views as the reach metric.
2. **Reuse the winner renderer.** Feed GambaTime's exact neural word timings into
   `clipforge.production.assemble`, with Kruz-owned footage first and strict
   official-publisher footage second. Select cached footage by premise/title
   relevance and recency, not the current date hash; the first local proof
   exposed the hash choosing an older Feybreak trailer despite the exact 1.0
   trailer being available. Keep b-roll audio unmapped.
3. **Keep the verified-news brand.** Preserve verdict, source outlets, premise
   identity, collision-safe edition IDs, Discord veto, resumable upload, public
   receipt, and affiliate disclosure.
4. **Protect the first ten seconds.** Run hook directly into verified player
   impact. Move generic subscription copy after the payoff or omit it; never
   interrupt the opening story with a channel pitch.
5. **Add a visual preflight.** Known-game stories must use story-specific safe
   footage, carry source video ID/title/channel plus attribution, and clear at
   least **20% motion frames** at two samples/second with scene threshold
   `0.03`. Gradient/default footage cannot satisfy the live gate.
6. **Generate a contact sheet.** Include six sampled frames, footage provenance,
   motion score, LUFS, and true peak in the Discord veto receipt.
7. **Create an honest commerce handoff.** On affiliate-eligible/player-impact
   stories, use a restrained final cue: `Game Deals on our profile`. Do not claim
   a discount or lowest price without current storefront evidence.
8. **Repair measurement.** Tag every automated redirect check with a `*-probe`
   source, archive the contaminated baseline, and rebaseline once without
   changing either public profile link or the Deals Hub surface.

## Seven-day proof

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

## Remaining Kruz-only action

In YouTube Studio, attach Related Video `qXfmFo0ih4I` to Short `_37uQjQ2xtg`.
That field is Studio-only. It improves the in-platform content path, but the
June recap needs a disclosed Deals Hub link in its long-form description before
it can serve as a revenue bridge.

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

## Decisive next command

Build ProofCut, run the full Clipforge suite, render one private/local matched
proof, inspect its contact sheet, and only then arm the next public canary.
