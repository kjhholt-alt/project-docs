# 01 — Style Teardown Framework

> Goal: turn "it just feels so good" into a **measurable spec** you can reproduce.
> You run this teardown once on each reference doc, then merge the results into a
> single **`STYLE_SPEC.md`** that becomes the rubric for every WWI episode.

## How the teardown works (the AI part you asked about)

1. **Transcribe** the reference doc with timestamps → `Whisper` (open-source) or
   any speech-to-text. Output: a timestamped transcript (the "what was said").
2. **Annotate** the transcript with a model (Claude) against the rubric below →
   the model labels each segment with its *function* (cold open, human anchor,
   strategic pullback, reflection, etc.) and notes the *technique*.
3. **Quantify** — have a small script (Claude Code builds it) compute the metrics:
   average shot/segment length, words-per-minute of narration, ratio of
   human-story to strategic-overview time, sentence length distribution.
4. **Synthesize** — the model writes the `STYLE_SPEC.md`: a reproducible
   description of the voice + a checklist any new script must pass.

This is exactly what an LLM is *built* for: it's pattern extraction over a
transcript, not "copying." You end up with a **recipe**, not their content.

## The rubric — score every reference doc on these axes

### A. Narration voice
- **Cadence / WPM** — words per minute; where it slows down (emotional beats) vs.
  speeds up (logistics, dates).
- **Sentence length** — short punchy vs. long winding; ratio.
- **Person & tense** — "On the morning of..." present-tense immediacy vs. reflective
  past tense. When does it switch?
- **Vocabulary register** — plain and concrete vs. literary. Does it explain jargon?
- **Silence** — how often it lets the score/footage breathe with no narration.
- **Emotional restraint** — does it state the horror plainly and let *you* feel it,
  rather than telling you how to feel?

### B. Story architecture
- **Cold open** — how the first 60–90s hooks. A person? A question? A sound?
- **The "human anchor → strategic pullback" cycle** — measure the rhythm: how long
  on the individual story before zooming out to the war's big picture, and back.
- **Act structure** — how a 40-min piece is divided. Where are the tension peaks?
- **Through-line** — is there a single character/letter/place that recurs?
- **The turn** — the mid-point reframe or reveal.
- **Landing** — how it ends. Reflection, a name, a number, a silence.

### C. Historian / expert usage
- **When experts speak vs. narrator** — what's reserved for the historian's voice
  (interpretation, "why it mattered") vs. the narrator (facts, sequence).
- **Carlin-style technique** — visceral sensory detail, "imagine you are there,"
  scale-of-horror framing, rhetorical questions. Catalog the specific moves.

### D. Sound & image (for the edit spec)
- **Score** — genre, instrumentation, dynamics; sits under voice, swells in silence.
- **Footage discipline** — average hold time on a shot; sparse vs. busy.
- **Maps & motion graphics** — how often, how simple.
- **Transitions** — hard cuts vs. slow dissolves; rhythm.

## Deliverable: `STYLE_SPEC.md` (template the teardown fills in)

```markdown
# WWI Documentary — Style Spec (derived, original execution)

## Voice
- Target WPM: [e.g. 110–130, slower on emotional beats to ~95]
- Sentence mix: [e.g. 60% short declaratives, 40% longer reflective]
- Tense rule: [present tense for scenes, past for analysis]
- Register: [plain, concrete, sensory; define jargon once]
- Silence rule: [≥1 breath of score-only every ~90s]
- Emotion rule: [state plainly, never editorialize the feeling]

## Story
- Cold open formula: [hook type + length]
- Human↔strategic cycle: [~2–3 min human, ~60–90s pullback, repeat]
- Act map for 30 min: [Act 1 setup / Act 2 escalation / Act 3 cost+meaning]
- Through-line: [recurring person/place/object]

## Expert layer
- Narrator owns: [sequence, facts]
- Historian owns: [meaning, "why it mattered", visceral immersion]

## Edit
- Avg shot hold: [Xs]
- Score: [style + dynamics rule]
- Maps: [frequency + simplicity rule]

## The 12-point checklist (every episode must pass)
1. Cold open hooks within 90s with a human or a question
2. ... (filled in from the teardown)
```

## ⚠️ Guardrail baked into the teardown

The teardown prompt explicitly instructs the model to **describe technique, not
transcribe content** — outputs are *patterns and rules*, never the reference
doc's sentences. See `prompts/teardown.md`.
