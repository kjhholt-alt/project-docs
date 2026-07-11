# GambaTime EvidenceCut Pilot Packet

**Prepared:** 2026-07-10/11
**Purpose:** Turn the channel-quality study into a rolling three-video treatment
while existing production and publishing continue at full cadence.

## Pilot decision

Produce three 28-38 second Shorts using the same editorial promise:

> The receipt, the player impact, and the verdict.

The pilot tests one change bundle because the current format needs a categorical
quality reset, not a small thumbnail or title optimization. The bundle is:

- one specific, verified player-impact story;
- one consistent rights-safe production voice, with human narration optional;
- owned or explicitly rights-cleared gameplay motion;
- three to six claim-matched evidence inserts;
- word-timed captions;
- an original verdict;
- no intro, generic subscription ask, or commerce CTA.

This packet does not pause or alter the existing scheduled producer/publisher.
It grants no cleanup, deletion, unlisting, or bulk metadata authority.

## Story admission gate

A story is eligible only when every hard gate passes.

| Hard gate | Pass condition |
| --- | --- |
| Primary receipt | First-party statement, filing, patch note, store page, court record, or direct interview is saved |
| Corroboration | A second credible source confirms the material claim or supplies essential context |
| Player consequence | The script can state exactly what changes for a player, buyer, worker, or focused game community |
| Honest specificity | The title can use a real number, decision, restriction, price, date, or consequence without exaggeration |
| Visual proof | At least three evidence moments can be shown when their claims are spoken |
| Motion rights | Background gameplay is Kruz-owned or has a current documented commercial-use basis |
| Freshness | The story is still timely when the video would publish |
| Original value | GambaTime can add a defensible implication or verdict rather than restating the source |

Rank eligible stories from 0-3 on each dimension:

1. Player impact
2. Specificity
3. Source strength
4. Visual proof
5. Focused-community intensity
6. Timeliness
7. Original judgment
8. Deals relevance, used only as a tie-breaker

Require at least 17/21 across the first seven dimensions. Deals relevance never
rescues a weak story.

## Script contract

Target 95-125 spoken words. Write for natural delivery, then time the recording;
do not speed a weak script merely to fit the limit.

| Time | Function | Required result |
| --- | --- | --- |
| 0-3s | Claim | State the exact surprising consequence, not a generic question |
| 3-10s | Receipt | Name or show the primary source and the decisive fact |
| 10-25s | Impact | Explain what changes and for whom; use the key number or contrast |
| 25-38s | Verdict | Add GambaTime's concise judgment or implication and end cleanly |

Draft shape:

```text
[CLAIM]
<Actor/decision> just caused <specific consequence>.

[RECEIPT]
<Primary source> says/shows <decisive fact>, while <corroboration> confirms
<necessary context>.

[PLAYER IMPACT]
That means <concrete effect> for <specific people>, because <mechanism>.

[VERDICT]
GambaTime verdict: <original, defensible conclusion>.
```

Script rejection conditions:

- `might`, `could`, or `reportedly` carries the central claim without explaining
  the uncertainty;
- the headline is stronger than the saved sources;
- the player impact is merely `fans are upset`;
- a listicle, roundup, or second story is needed to fill the runtime;
- a spoken citation and rendered source label do not match the approved bundle;
- any subscribe, comment, affiliate, or profile ask appears in the pilot copy.

## Narration modes

The automatic lane uses one consistent, rights-safe synthetic voice so narration
never blocks throughput. Do not clone or imitate another person. Every automatic
edition still passes canonical-script alignment, loudness, silence, and caption
QC.

Human narration is an optional tagged hero variant, not a gate. When used, a
phone or ordinary desktop microphone is sufficient because consistency and
human authorship matter more than studio polish.

Recording contract:

- quiet room, microphone 15-25 cm away;
- one natural read plus one optional alternate;
- no background music, voice effects, or manual noise removal;
- leave about one second of room tone at the beginning and end;
- say the wording naturally, but flag any changed fact or number;
- deliver WAV, M4A, or lossless phone audio with the edition ID in the filename.

The pipeline owns cleanup, loudness normalization, transcription, word
alignment, captions, and final audio QC. It must never synthesize missing words
in Kruz's voice or imitate another creator.

The approved script remains the caption and factual source of truth. ASR supplies
timing evidence only: align its tokens back to the canonical script, save a
readable diff, and block unresolved additions, omissions, numbers, dates, or
proper-name substitutions. Never render raw ASR text directly.

## Evidence cue sheet

Every material spoken claim maps to a saved visual receipt. Evidence is shown
when the claim is spoken, not collected into an unreadable end card.

| Cue | Timing | Visual | Rule |
| --- | --- | --- | --- |
| E1 | Claim | Actor, product, or decision identity | Establish what the story is about immediately |
| E2 | Receipt | Cropped primary-source excerpt | Keep the decisive phrase and source identity legible |
| E3 | Number | Large isolated number/price/date | It must be present in the underlying receipt |
| E4 | Impact | Relevant product or player-facing state | Show the consequence rather than generic motion |
| E5 | Context | Corroborating source or before/after contrast | Use only when needed to avoid misleading framing |
| E6 | Verdict | Story-specific visual callback | End on the subject, not channel chrome |

Source crops must preserve enough context to identify the publisher/account,
date, and quoted language. Decorative screenshots do not count as evidence.

## Render and QC gate

The local render passes only when all checks pass:

- 1080x1920, 28-38 seconds, and no black/freeze/silence failure;
- integrated loudness and true peak remain inside the established GambaTime
  delivery bounds;
- word captions match the human recording and never cover source identity or
  the decisive text in an evidence crop;
- canonical-script alignment passes, with every changed number, date, and
  proper name explicitly resolved;
- three to six evidence cues appear at their intended spoken claims;
- background footage audio is absent;
- footage provenance, rights basis, source URLs, script hash, narration hash,
  and final render hash are in the receipt;
- six-frame contact sheet is readable at phone scale;
- a reviewer can answer `what happened`, `who it affects`, and `what GambaTime
  thinks` after one watch;
- the title, first sentence, evidence, and verdict describe the same claim.

Any factual, rights, or source/render-parity failure blocks the artifact rather
than being waived for freshness.

## Release design

Use three distinct stories and hold the EvidenceCut treatment constant, but keep
all ordinary production running. Tag every renderer, narration mode, topic,
script pattern, and evidence-cue count with collision-safe experiment IDs.
Publish treatments as soon as their existing source, rights, QC, and publish
gates pass. A video reaching 72 hours triggers its first grade; it never blocks
the next render or release.

Measure each at 72 hours and again at seven days:

| Metric | Current reference | Pilot median pass |
| --- | ---: | ---: |
| Engaged views / raw starts | 42.1% | at least 53% |
| Likes / raw views | 0.95% | at least 1.9% |
| Raw views | 456 | at least 900 |
| Attributed subscribers | none required per video | at least 1 across three |
| Factual/rights/QC failures | unacceptable | zero |

Also record average view duration, retention curve, traffic source, profile
entries, Deals Hub outbounds, and any network-confirmed commission. Do not count
GambaTime's own pinned comment as audience engagement, and do not mix automated
probe traffic with buyer behavior.

## Decision after video three

**Scale EvidenceCut** when the first tagged cohort clears the engaged/start and
likes/view thresholds with zero integrity failures. Promote it from shadow mode
to 25%, then 50%, then 100% of eligible stories as rolling cohorts confirm the
gain. Total throughput does not decrease during rollout.

**Iterate once** when retention improves materially but likes or subscribers do
not. Review topic strength and verdict quality before changing captions or edit
tempo.

**Roll back the treatment, not production** when engaged/start remains below
47%, any integrity gate fails, or the stories cannot be distinguished from
commodity news reads. The last verified renderer continues while the failed
variation is diagnosed in shadow mode.

Consider a new channel only after ten EvidenceCuts improve audience-quality
signals while distribution remains consistently suppressed. Before then, the
existing channel's history, OAuth, advanced features, attribution, and profile
funnel are assets worth preserving.

## Cleanup lane

Separately prepare a reversible visibility manifest for exact duplicate uploads,
unsupported stories, and generic videos below 500 views. A zero-subscriber
candidate requires an available Analytics row confirming the attribution and at
least 72 hours of age; missing Analytics data never means zero. The manifest
must identify a retained copy and total views at risk. It is a review artifact
only: no deletion, unlisting, or bulk title changes are part of the creative
pilot.

## Implementation order

1. Add canonical-script alignment for both synthetic and human narration.
2. Add timed evidence-cue overlays backed by the canonical source bundle.
3. Emit one manifest containing source, rights, narration, cue, experiment, and
   QC receipts.
4. Shadow-render a matched proof while current production remains live.
5. Promote the passing treatment into 25% of eligible output, then ratchet the
   share upward from rolling cohort evidence.
6. Add optional human hero variants whenever narration takes are available.

The next engineering artifact is the local matched EvidenceCut proof. There is
no Kruz narration dependency for the automatic lane.
