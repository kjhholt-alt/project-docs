# SAGA ENGINE (gl-0084) — Verify-First Scout Report

*Cloud lane, 2026-07-05 (night shift). Kruz picks a theme from §5 in the morning. Companion: the Phase-1 spec (SAGA-ENGINE-SPEC.md) built on this.*

**Verdict up front: GO-WITH-CAVEATS.** The deterministic generate→validate loop the thesis needs **exists and is better than hoped** — but it's two-stage (offline linter catches ~90%, game-load catches the rest), and the "fired in-game" step is the only part that can't be fully headless.

---

## 1. CK3 Modding Stack 2026

**Game version:** 1.19 "Scribe" (2026-04-20); hotfix 1.19.0.6 (2026-05-25). Chapter V expansion pass live with more content later in 2026 — **expect a 1.20-class patch this year** ([Patches](https://ck3.paradoxwikis.com/Patches), [Patch 1.19](https://ck3.paradoxwikis.com/Patch_1.19)).

**Patch churn risk — real but LOW for this mod shape.** Flavor/event mods are the most patch-insulated category (no achievement/save invalidation since 1.9). What breaks: GUI mods, total conversions, vanilla-file overrides — which the reference flavor mod (RICE) deliberately avoids by never editing vanilla files ([RICE GitHub](https://github.com/cybrxkhan/RICE-for-CK3)). A self-contained events+decisions+loc mod mostly needs its `supported_version` bumped.

**Structure (verified):** `Documents/Paradox Interactive/Crusader Kings III/mod/` — `.mod` descriptor + folder with `events/`, `common/`, `localization/english/`. The game self-documents: console `script_docs` + `dump_data_types` dump **every trigger/effect/scope/on_action for the exact installed version** — a machine-readable grounding corpus regenerable after every patch ([Console commands](https://ck3.paradoxwikis.com/Console_commands)).

## 2. THE VALIDATION LOOP — thesis confirmed, two-stage

**Stage 1 — offline, headless, deterministic: `ck3-tiger`.** The load-bearing find. Mature Rust CLI linter (2,589 commits): syntax errors, misspelled keys, undefined references, **missing localization keys**, **scope-type mismatches**. Runs offline against `descriptor.mod`, no game process. **v1.19.0 released 2026-06-06 — tracks the current game patch within ~6 weeks** ([amtep/tiger](https://github.com/amtep/tiger)). Community VS Code extension surfaces it in Problems. (CWTools still alive for autocomplete; tiger is the validator of record — the wiki's own troubleshooting page recommends it.) **Agent loop: generate → `ck3-tiger mymod.mod` → parse → fix → repeat. Fully scriptable, zero game launches.**

**Stage 2 — game-load validation.** CK3 validates all script databases at boot → deterministic `logs/error.log`; `-debug_mode` pops "Errorhoof" listing script errors. No headless mode — but **loading to main menu triggers full validation**, and `-debug_mode -develop` gives **hot-reload** (`reload events`, `reload localization`) + console (`event saga.1`, `run file.txt`, `observe`) so iteration doesn't need restarts.

**Big-mod QA reality:** RICE/AGOT develop on GitHub, no formal CI found — **an agent fleet running tiger on every commit would EXCEED community-standard QA.** Caution: big mods' error.logs grow huge — gate on *zero new errors in our namespace*, not zero globally.

## 3. Content Anatomy — one saga, no new art

- One event: `namespace = saga`; `saga.1 = { type = character_event, title/desc = loc keys, theme = <vanilla>, left_portrait, trigger, immediate, option = {...} }`. Chaining via `trigger_event = { id = saga.2 days = { 7 14 } }`. Entry: decisions or vanilla **on_actions** — and **on_action event lists merge additively across mods** (no vanilla overwriting, no mod conflicts).
- **Art: zero new assets needed — verified.** `theme = X` auto-assigns vanilla background/sound/environment; portraits live-render from character DNA with vanilla animations.
- **Localization:** `localization/english/saga_l_english.yml`, **UTF-8 WITH BOM**, `l_english:` header. English-only fine for v1 (no fallback for other languages; trivial copy-script later).
- **20-event saga effort shape:** ~3-5 event files (1,500-2,500 lines script), ~120-200 loc keys, 1-2 decisions, one on_action hook file, scripted_effects for shared logic. Script is the cheap part — **loc prose quality (the fleet's strength) and pacing/balance (Kruz's play-gate) are the expensive parts.**

## 4. Agent-Friendliness + IP

- **LLM-tractability: HIGH.** Small regular grammar; game emits its own symbol tables; offline validator; big public corpus (vanilla events/ ship readable; RICE open-source). Two agent traps: UTF-8-BOM enforcement, scope discipline (tiger catches).
- **IP:** Paradox UGC = encouraged, free/non-commercial. No explicit Paradox AI-content ban found (one forum thread unreadable behind Cloudflare — honest unknown). Steam's 2026-01 AI policy requires disclosure for player-consumed AI content in shipped products — moot for personal use; disclose if ever published. Text-only sidesteps the AI-art controversy entirely.

## 5. THEME SHORTLIST — Kruz picks one

1. **The Withering Crown** — dynasty-curse arc: each generation inherits an escalating hidden curse to fight, embrace, or transact away. *Pure dynasty drama over centuries; mechanically simple (flags + on_birth/on_death).*
2. **The Quiet Hand** — shadow-court/secret-society line: recruited, climb, discover it steers succession across realms; betray or inherit it. *Systems-brain meets intrigue; heaviest mechanical footprint — the "gamest" option.*
3. **Salt and Silver** — merchant rags-to-throne saga on 1.19-era landless play: one ship → buying a crown. *The tycoon pillar (~900h) fused into CK3; most novel tone.*
4. **The Nail of the True Cross** — crusader-relic mystery: contested provenance; authenticate, forge, or weaponize as claims and heresies bloom. *Cascading consequence web; strong replay via branching truth.*
5. **The Years of Ash** — plague-era survival/legacy arc: enduring, not winning — quarantine choices, succession under mortality pressure, chronicler events memorializing loss. *Elegiac tone; light mechanics, prose-heavy — plays to the fleet's writing strength.*

## Verdict: GO-WITH-CAVEATS — ranked unknowns

1. **No validator for FUN** — tiger proves correctness, not pacing. Mitigation: Kruz play-gate per saga.
2. **Game-load stage needs a real CK3 launch** — automatable on his box (launch `-debug_mode`, wait, diff error.log, kill), but it's a local-GUI lane, never cloud; respects no-popups-while-gaming.
3. **1.20-class patch expected** — descriptor bump + tiger re-run + `script_docs` re-dump; low risk, schedule it.
4. **Paradox AI stance unconfirmed** — re-check only if publishing.

**Phase-1 hello-world (exact):** (1) mod skeleton + descriptor `supported_version="1.19.*"`; (2) one `saga.1` event + loc file (UTF-8 BOM); (3) `ck3-tiger` → zero namespace findings; (4) launch `-debug_mode -develop` → no new error.log lines, no Errorhoof; (5) console `event saga.1` → renders with vanilla art; screenshot = proof; (6) run `script_docs` once, commit dumps as the agent grounding corpus.

*(Full source list in lane transcript; primary links inline.)*
