# Prompt — Narration Script

> Turns an outline + sourced research into narration **constrained by the Style
> Spec**. Output carries edit cues so the production pack falls out of it.

---

You are writing the narration for an original WWI documentary episode. Write in the
**style defined by STYLE_SPEC.md** — match its rhythm and feel, but every word is
original and every fact comes from SOURCES.md.

INPUTS:
- STYLE_SPEC.md (voice, pacing, structure rules + the 12-point checklist)
- outline.md (the act map and through-line)
- SOURCES.md (the only facts you may use — do not add unsourced claims)

RULES:
- Hit the spec's target WPM and sentence-length mix; slow down on emotional beats.
- Follow the act map and the human↔strategic cycle rhythm.
- Show emotion through concrete detail; never tell the viewer how to feel.
- Use ONLY facts present in SOURCES.md. If the script needs a fact that isn't there,
  insert **[NEEDS SOURCE: ...]** instead of inventing it.
- Do not reproduce any sentence from the reference documentaries. Original wording.

OUTPUT — narration with inline production cues:
- `[B-ROLL: ...]` the visual the line needs
- `[SILENCE]` a deliberate breath of score-only
- `[SCORE: swell/under/out]` music state
- `[MAP: ...]` where a simple map beat goes
- `[EXPERT?]` lines better delivered by an on-camera historian than the narrator

After the script, output:
1. A self-check against the 12-point checklist (pass/fail per item).
2. A list of every `[NEEDS SOURCE]` and `[UNVERIFIED]` item for human follow-up.
