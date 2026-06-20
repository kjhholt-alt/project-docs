# 02 — AI Production Pipeline

> "Pick a WWI topic → finished 20–40 min documentary" with human checkpoints at
> the three places that matter: **research accuracy, script tone, final cut.**
> Claude Code builds and orchestrates the tooling; Codex/Claude write the glue.

## The pipeline at a glance

```
[0] Topic        -> pick from episode backlog
[1] Research     -> AI gathers + summarizes WWI sources   ⟶  HUMAN CHECK (facts)
[2] Outline      -> AI structures via STYLE_SPEC act map
[3] Script       -> AI drafts narration in the learned voice ⟶ HUMAN CHECK (tone)
[4] Source pack  -> AI assembles citations + b-roll shot list
[5] Visuals      -> pull public-domain footage/photos + maps + (labeled) AI b-roll
[6] Voice        -> TTS draft now; pro VO / licensed voice later
[7] Edit         -> AI builds an Edit Decision List; you assemble
[8] Package      -> AI writes title/desc/chapters/thumbnail brief ⟶ HUMAN CHECK (final)
[9] Publish + measure -> retention scorecard feeds back into STYLE_SPEC
```

## Stage by stage

### [1] Research
- **Tool:** Claude (long-context) + web research; store sources in a per-episode
  `research/` folder with a `SOURCES.md` (URL, author, date, key facts, quote +
  exact citation).
- **Rule:** every claim that will appear on screen must trace to a source line.
- **Checkpoint:** you skim `SOURCES.md` and flag anything shaky before scripting.
- **Anti-hallucination:** the research prompt forbids invented quotes/dates and
  requires "UNVERIFIED" tags on anything the model can't source.

### [2] Outline
- Model maps the topic onto the `STYLE_SPEC` act structure (cold open → human↔
  strategic cycles → cost/meaning landing) and proposes the through-line
  (a recurring soldier, letter, place, or object).

### [3] Script (the narration)
- Model drafts narration **constrained by `STYLE_SPEC`**: target WPM, sentence
  mix, tense rules, silence beats, emotional restraint.
- Output includes inline **[SILENCE]**, **[SCORE SWELL]**, and **[B-ROLL: ...]**
  cues so the edit spec falls out of the script.
- **Checkpoint:** read it aloud. If it doesn't *sound* like the references in
  rhythm (not words), it fails the 12-point checklist — send back with notes.

### [4] Source pack
- Model emits: citation list (for description + on-screen credits), a **shot list**
  mapping each narration beat to a needed visual, and a music-cue map.

### [5] Visuals
- **Public-domain WWI footage/stills:** Library of Congress, US National Archives,
  Imperial War Museum (check non-commercial vs. commercial terms), Europeana,
  Wikimedia Commons (verify each license), Critical Past / Internet Archive.
- **Maps & motion graphics:** simple animated maps (After Effects template or a
  tool like Mapbox/Blender) — kept sparse per the spec.
- **AI b-roll:** allowed for *atmosphere* (mud, rain, a trench at dawn) but **must
  be labeled** as illustration, never passed as authentic footage. Keep a clear
  on-screen/disclosure convention.

### [6] Voice / narration
- **Now (cheap):** high-end TTS for a draft so you can cut the whole thing before
  paying for VO. Use it to test pacing.
- **Later (quality):** a single human VO artist, or a properly **licensed** AI
  voice. Never clone the reference narrator's voice.
- Feed the TTS the script's pacing cues; tune WPM to the spec.

### [7] Edit
- Model produces an **Edit Decision List (EDL)**: ordered narration lines ↔ shot ↔
  duration ↔ music state. You (or an editor) assemble in Resolve/Premiere.
- A Claude Code script can lint the EDL: flag shots held too short vs. the spec,
  narration with no assigned visual, etc.

### [8] Package
- Model drafts: 3–5 title options (curiosity + clarity), description with sources,
  timestamped chapters, a thumbnail creative brief, pinned-comment sources note.
- **Checkpoint:** final watch-through against the 12-point checklist + fact spot-check.

### [9] Measure → feed back
- After publish, log **audience retention** (where viewers drop), CTR, and comment
  sentiment into a per-episode scorecard. The biggest retention dips tell you which
  `STYLE_SPEC` rules to tighten. The spec is a living document.

## The toolchain (what each tool is for)

| Need | Tool | Notes |
|---|---|---|
| Orchestration, scripts, glue | **Claude Code** | builds the repo: research/outline/script/EDL tooling |
| Code generation alt | **Codex** | fine for boilerplate; Claude Code is the driver here |
| Transcribe references | **Whisper** | timestamped transcripts for the teardown |
| Drafting (research, script, QA) | **Claude API** (long context) | the writing + reasoning engine |
| Narration draft | **TTS** (e.g. a quality neural voice) | swap for human VO at quality bar |
| Visuals | public-domain archives + maps + labeled AI b-roll | see Stage 5 |
| Edit | Resolve (free) / Premiere | assemble from the EDL |
| Measure | YouTube Studio analytics | retention → scorecard |

## Where humans stay in the loop (non-negotiable)
1. **After research** — is it *true* and sourced?
2. **After script** — does it *feel* right and is it accurate?
3. **Before publish** — final watch, fact spot-check, disclosure check.
