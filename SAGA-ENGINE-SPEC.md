# THE SAGA ENGINE — Phase 1 Spec (binding)

*Fable, 2026-07-05 night shift. Grounded in [SAGA-ENGINE-SCOUT.md](SAGA-ENGINE-SCOUT.md) (GO-WITH-CAVEATS; ck3-tiger = the offline validation loop). Project: gl-0084. Repo home: new private repo `saga-engine` (mod source + pipeline tooling; the mod folder deploys to `Documents/Paradox Interactive/Crusader Kings III/mod/`).*

**THEME: ___ (Kruz picks 1-5 from the scout — everything below is theme-agnostic; the style bible instantiates after the pick).**

## North star

A Paradox-DLC-scale flavor expansion **written by the fleet, validated by machine, paced by Kruz.** Hundreds of interlocking events with the prose quality of a good DLC — where "verified" means: tiger-clean, error.log-clean, fired-in-game, and **fun-gated by the only player who counts.**

## The pipeline (the actual product)

```
0. GROUNDING     script_docs + dump_data_types from the live game →
                 committed corpus (regenerate on every game patch)
1. STYLE BIBLE   Fable writes after theme pick: voice, tone, register,
                 period vocabulary, option-writing rules (no modern
                 idiom, options imply consequence, 2-4 options/event),
                 arc grammar (setup→complication→payoff per 5-7 events)
2. ARC DESIGN    Fable: saga outline — chains, entry hooks (decisions /
                 on_action pulses), state variables, branch map
3. GENERATION    Sonnet lanes: one chain per lane (events + loc +
                 scripted_effects), file-disjoint by namespace block
4. GATE A        ck3-tiger — ZERO findings in our namespace (scriptable,
                 offline, every commit; agent fixes loop until clean)
5. GATE B        game-load lane (LOCAL, posture-aware, away-window only):
                 launch -debug_mode → diff error.log (zero new lines in
                 our namespace) → console `event saga.N` smoke per entry
                 event → screenshot proof → kill process
6. GATE C        Kruz play-gate per saga: real campaign, honest verdict.
                 Fun has no validator; he is the validator.
```

**Rails (from the scout, binding):**
- NEVER edit vanilla files (RICE rule); on_action hooks are additive-merge only.
- Localization writer enforces UTF-8 **with BOM**, `l_english:` header. English-only v1.
- Zero new art: vanilla `theme =` backgrounds/portraits/animations only.
- Gate on *our-namespace* errors, never global error.log noise.
- Descriptor pinned `supported_version="1.19.*"`; on the expected 1.20-class patch: bump, re-dump grounding corpus, full tiger re-run (scheduled, not reactive).
- Steam Workshop publication = separate Kruz decision later (AI disclosure per Steam policy if ever published; personal use v1).

## Phases + verify bars

**P1 — hello world (one lane, tonight-ready except the game-launch step).**
Exactly the scout's ladder: mod skeleton → one `saga.1` event + loc → tiger zero-findings → `-debug_mode` load clean → console-fire screenshot → commit `script_docs` dumps as the grounding corpus.
*Verify: the screenshot of saga.1 rendering with vanilla art + tiger output showing 0 our-namespace findings.*

**P2 — the style bible + first arc design (Fable, after theme pick).**
*Verify: Kruz reads the bible + outline and says "that's the tone" before mass generation.*

**P3 — first saga: one complete 20-event chain.**
3-5 event files, ~120-200 loc keys, 1-2 decisions, one on_action hook file, arc state via character flags/variables. Generated in parallel file-disjoint lanes, both machine gates green.
*Verify: Kruz experiences the full chain in a real campaign and gives the fun verdict. That verdict decides P4 scale-up.*

**P4+ (gated on P3 fun-verdict):** parallel saga production (the "DLC" — 5-8 interlocking chains), cross-chain callbacks (a character from saga A resurfacing in saga C), chronicle events that memorialize earlier player choices.

## Division of labor

- **Fable:** theme instantiation, style bible, arc outlines, final prose pass on flagship events, fun-gate triage with Kruz.
- **Sonnet lanes:** event scripting + first-draft prose + loc files + tiger-fix loops (the volume).
- **Local away-window lane:** Gate B game-load runs (the only step needing the real game).
- **Kruz:** theme pick, tone sign-off, play-gates. Nothing else.

## Decisions for Kruz (morning)

1. **THE THEME** (1-5 — posted to Discord).
2. Repo privacy default: private (recommended) — Workshop question deferred.
