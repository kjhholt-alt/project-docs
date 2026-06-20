# 00 — Strategy & The Bet

## The thing you noticed

The Tom-Hanks-narrated WWII documentary (with Dan Carlin and other serious
historians) feels *so good* because of a stack of craft decisions, not luck:

- **Narration tone** — calm, intimate, unhurried, lets silence breathe. Authority
  without melodrama.
- **Story selection** — it picks the *human* moment over the troop-movement map.
  One letter, one soldier, one decision, then it pulls back to the strategic
  picture.
- **Pacing / structure** — long-form, but it never feels long. Tension → release →
  reflection, on a repeating cycle.
- **Credibility** — real historians (Carlin et al.) give it weight; their
  storytelling cadence is itself a style you can study.
- **Sound + image discipline** — score sits *under* the voice, archival footage is
  used sparingly and held longer than you'd expect.

None of that is magic. It's a **repeatable pattern**, and patterns are exactly
what an LLM is good at extracting and reproducing.

> 📌 **Reference doc — fill this in:** confirm the exact title/platform/year of
> the Tom Hanks WWII doc you love, plus 2–3 Dan Carlin "Hardcore History" WWI
> episodes (e.g. *Blueprint for Armageddon*). These are your **style references**.
> We study them; we never reuse their words or audio.

## The bet

One operator, with an AI-assisted pipeline, can produce **long-form WWI
documentaries** that *feel* like the premium WWII docs — because the hard,
expensive parts (research depth, narrative structuring, narration writing,
pacing) are exactly where current models are strong, and the cheap parts
(public-domain WWI footage, royalty-free score, TTS or a single VO artist) are
the only real costs.

WWI is also **under-served** on YouTube relative to WWII, the archival footage and
photos are overwhelmingly **public domain** (pre-1929), and the centenary content
wave has died down — there's an opening.

## What AI actually does here (be honest)

| AI is GREAT at | AI is OK-with-supervision at | AI should NOT do alone |
|---|---|---|
| Transcribing the reference doc (Whisper) | Drafting narration in the learned voice | Final fact-checking on history |
| Decoding structure/pacing into a rubric | Suggesting story beats & cold opens | Claiming primary-source quotes are real |
| Researching & summarizing WWI events | Writing video descriptions / SEO | Generating "archival" footage as if real |
| Building the production tooling (Claude Code) | First-pass edit decision lists | Anything implying it's authentic footage |
| QA: continuity, anachronisms, tone drift | TTS narration draft | Replacing a historian's judgment |

**Hard rule on accuracy:** every historical claim gets a source. The model drafts;
a human (you) + a citation check verifies. A documentary that's confidently wrong
dies fast in the comments.

## The legal / ethics line (do not cross)

- ✅ **Mimic the *method and feel*** — pacing, tone, structure, the "human moment
  then strategic pullback" rhythm. Style is not copyrightable.
- ✅ **Original script** — written from scratch about WWI events.
- ✅ **Public-domain visuals** — WWI footage/photos (pre-1929 US), Library of
  Congress, IWM (check terms), national archives, Europeana.
- ✅ **Licensed or royalty-free score** + your own VO or a paid TTS/voice license.
- ❌ **Do not** copy their script, lift their voice clone, reuse their footage, or
  reproduce a historian's exact narration. That's not mimicry, that's infringement.
- ⚠️ **AI-generated visuals** that look like real archival footage must be labeled.
  Misrepresenting AI imagery as authentic historical footage is the fastest way to
  lose trust (and run afoul of YouTube's synthetic-media disclosure rules).

## What "good" looks like (success criteria)

- A reusable **Style Spec** (the teardown rubric, filled in) that any episode can
  be measured against.
- A **pipeline** where "pick a WWI topic → finished 20–40 min video" is mostly
  push-button with human checkpoints at research, script, and final cut.
- **Episode 1 shipped** and indistinguishable in *feel* (not content) from the
  references to a casual viewer.
- A **scorecard** per episode: tone match, pacing, factual sourcing, retention.

## Working titles for the channel (pick later)

- *The Great War, Up Close*
- *Verdun* (codename — maybe too narrow as a channel name)
- *Letters From the Trenches*
- *1914* / *The War to End All Wars*
