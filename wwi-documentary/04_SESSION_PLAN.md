# 04 — Build Session Plan (for Claude Code)

> Same format as your other projects in `PROJECTS.md`. Each session is a single
> Claude Code sitting that produces a concrete artifact. Sessions 1–4 build the
> *system*; sessions 5–7 produce *Episode 1*.

**Suggested repo:** `github.com/kjhholt-alt/wwi-doc-engine`
**Stack:** Python (orchestration + scripts), Claude API (long context), Whisper
(transcription), a neural TTS for drafts. Markdown-driven, like your playbooks.

| Session | Task | Output | Status |
|---|---|---|---|
| 1 | **Teardown harness** | `transcribe.py` (Whisper) + `teardown.py` that runs `prompts/teardown.md` over a transcript and emits a draft `STYLE_SPEC.md` | ⬜ |
| 2 | **Run the teardown** | Filled-in `STYLE_SPEC.md` + the 12-point checklist, from the Tom Hanks doc + 1–2 Carlin WWI episodes | ⬜ |
| 3 | **Research + outline tooling** | `research.py` (sources → `SOURCES.md` w/ anti-hallucination rules) + `outline.py` (topic → act map) | ⬜ |
| 4 | **Script + package tooling** | `script.py` (outline + STYLE_SPEC → narration w/ cues), `package.py` (titles/desc/chapters), `edl_lint.py` | ⬜ |
| 5 | **Episode 1 — research** | `episodes/ep01-the-christmas-truce/research/SOURCES.md`, human-checked | ⬜ |
| 6 | **Episode 1 — script** | `narration.md` passing the 12-point checklist, read aloud | ⬜ |
| 7 | **Episode 1 — production pack** | shot list, EDL, music cues, package; ready to edit | ⬜ |

## Session 1 — exact kickoff prompt

```
We're building the WWI Documentary Engine. Read the wwi-documentary/ docs in
project-docs for full context.

This session: build the TEARDOWN HARNESS.
1. transcribe.py — wrap Whisper to produce a timestamped transcript from an
   audio/video file. CLI: python transcribe.py input.mp4 -> transcript.json
2. teardown.py — take a transcript + prompts/teardown.md, call the Claude API,
   and emit a draft STYLE_SPEC.md following the template in 01_STYLE_TEARDOWN.md.
   The prompt MUST extract patterns/rules only, never reproduce sentences.
3. Add a README, requirements.txt, .env.example (ANTHROPIC_API_KEY), and a smoke
   test with a tiny sample transcript.
Use the latest Claude model. Keep it simple and runnable. Show me it works.
```

## Costs (rough, monthly, solo)

| Item | Est. | Notes |
|---|---|---|
| Claude API | low–moderate | research + scripting; long context but few episodes/mo |
| Whisper | ~free | runs locally / open source |
| TTS (draft) | low | until you move to human/licensed VO |
| Human VO (later) | per-episode | the main quality upgrade cost |
| Music | low | royalty-free subscription |
| Footage | ~free | public domain, if sourced carefully |
| Editor (optional) | per-episode | or DIY in DaVinci Resolve (free) |

The expensive resource is **your review time** at the three checkpoints, not money.

## Definition of done for the build phase
- [ ] `STYLE_SPEC.md` exists and an episode can be scored against it
- [ ] Running the pipeline on a topic yields a sourced script + production pack
- [ ] Episode 1 is edit-ready and passes the 12-point checklist
