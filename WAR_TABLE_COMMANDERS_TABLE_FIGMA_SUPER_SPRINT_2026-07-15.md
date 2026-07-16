# The War Table — “The Commander’s Table”

## Figma super-sprint specification

- **Decision date:** 2026-07-15
- **Status:** selected next design bet; design-only until Kruz approves the Figma prototype
- **Product:** `C:\Users\Kruz\Desktop\Projects\war-table`
- **Distribution:** local working spec; do not push from the public `project-docs` remote
- **Design thesis:** a tactile command instrument where every plan change is measured honestly—especially when it changes the odds

---

## 1. Decision

The next premium Figma sprint should go to **The War Table**, not another portfolio surface or operator dashboard.

The game already has the scarce part:

- a deterministic combat engine with more than 1,000 tests;
- a playable deploy → fight loop in Unity;
- real roguelike depth: recruits, objectives, relics, economy, reserve, abilities, weather, morale, orders, and run records;
- an unusually strong differentiator in the Preview Oracle: the player can change a plan and see how robust it is across a deterministic seed sample;
- a real-player path: Kruz can play it directly on desktop, with a credible Steam Deck target once the audited input foundation lands.

The presentation is now the bottleneck. Current screenshots show a bright board surrounded by prototype panels, overlapping labels, tiny typography, weak phase hierarchy, and several information bands competing with the table. The game’s deepest systems are either invisible or represented as raw text. The current Unity UI is spread across independent, code-built uGUI canvases that all scale from a 1920×1080 reference, even though the intended floor is Steam Deck.

This is exactly the asymmetry a serious Figma sprint should attack: **do not add more game; make the existing game legible, desirable, and coherent.**

---

## 2. Product promise

> Read the situation. Shape a plan. Watch the odds move. Commit. Live with the result.

The player fantasy is not “manage a dashboard.” It is **command a miniature force on a physical table whose systems are honest enough to show why a plan works or fails.**

The experience should make three things feel exceptional:

1. **Spatial planning** — placing a unit should feel consequential, not like dragging a generic card.
2. **Orders and forecast** — a stance or target-priority change should visibly alter the plan and the Oracle.
3. **Commitment** — pressing Commit Orders should feel like sealing a tactical decision, followed by a combat presentation that gets out of its own way.

---

## 3. Why this wins now

### Highest marginal design value

War Table’s engine is far ahead of its interface. A premium interaction system exposes already-built value instead of inventing more scope.

### Real pull

This is a game Kruz can run and play on desktop. The spec turns Steam Deck from an aspiration into a gated input, layout, and real-device proof path. It satisfies the player-first rule better than another abstract cockpit.

### Compounding system

The same tokens and components can later serve:

- the main roguelike expedition;
- Arena mode;
- mission briefings;
- the run map;
- shop, recruit, relic, and augment drafts;
- Battle of the Day capture;
- replay and After Action surfaces;
- store screenshots and a future demo.

### Strong proof path

The current project already produces deterministic headless Unity renders. We can compare the approved Figma composition against real planning, combat, and result renders without touching the combat engine.

### Baseline evidence pack

Import these checked-in captures onto `01 / Current-state audit` before drawing a polished frame. They are evidence and stress cases, not visual references to imitate.

| Capture | What it proves | Design response |
|---|---|---|
| `unity/_renders/arena_hud.png` | The current worst-case shell allows status bars, trays, nameplates, inventory, and actions to compete with the table. Labels overlap the world and the final action is visually detached from planning. | Use it as the density failure case. Arena is out of the first slice, but shared components must not recreate this stacking behavior. |
| `unity/_renders/play_wide.png` | The table, miniatures, room, and moving-camera foundation can carry the fantasy when the UI is absent. It also exposes large dead areas and an over-dominant practical light. | Preserve the world-first composition; design rails that frame rather than bury it. Flag lighting as a later feel-tuning dependency, not a Figma paint-over. |
| `unity/_renders/play_tight.png` | Combat framing changes substantially, so fixed overlays can collide with the active engagement. | Prototype the compact combat HUD against both wide and tight crops. |
| `unity/_renders/combat_beauty.png` | Emissive impact effects and damage numbers can obscure silhouettes and exceed the table's material range. | Cap combat UI/effect brightness and keep damage feedback out of unit faces and aim lines. |
| `unity/_renders/arena_result.png` | A result banner alone does not explain cause, forecast, objective outcome, or the next meaningful decision. | The After Action frame must teach, then route the player forward. |
| `unity/_renders/botd/f_0010.png` | The existing battle can survive a vertical crop, creating a future capture lane. | Keep the interaction core crop-resilient, but do not spend this sprint designing the vertical publishing surface. |

At implementation kickoff, regenerate the planning, combat-wide, combat-tight, and result captures from the branch being integrated. Date and commit-label them in the Figma audit page so an old render cannot silently become the visual source of truth.

---

## 4. The experience loop

```text
RUN MAP
   ↓ choose engagement
MISSION BRIEF
   ↓ inspect objective and opposition
DEPLOY
   ↓ place units
ORDER
   ↓ stance / target priority / conditional trigger
FORECAST
   ↓ Oracle shows odds, casualties, and dominant threat
COMMIT
   ↓ plan becomes sealed
RESOLVE
   ↓ UI retracts; combat reads clearly
AFTER ACTION
   ↓ learn why, keep survivors, choose the next route
```

This loop is the Figma prototype. The file is not complete until it can be clicked through from Run Map to After Action without explanatory narration.

---

## 5. Design direction: “Quiet Command”

### Character

- tactile, sober, precise;
- field instrument rather than sci-fi HUD;
- modern military information design without cosplay or faux classified-folder decoration;
- physical materials used sparingly: smoked metal, dark felt, map paper, brass hardware, acetate overlays;
- dense where the player is comparing; quiet where the player is watching.

### Explicitly avoid

- neon, glow, cyberpunk purple, or holographic blue;
- glassmorphism and floating translucent card walls;
- oversized mobile-style cards;
- tiny all-caps text as the primary hierarchy;
- “AI dashboard” styling;
- permanent UI on every edge;
- bright mint felt or overexposed board lighting;
- decorative military jargon that does not explain a decision;
- a wholesale 3D art rework during the interface sprint.

### Signature idea

The **Oracle acetate** is the signature component: a thin analysis layer that can slide over the right edge of the table during planning, compare the current plan to the previous plan, and then physically retract when the player commits. It makes the deterministic engine visible without turning the game into a spreadsheet.

---

## 6. Screen architecture

### A. Run Map

Purpose: choose the next engagement and understand the run at a glance.

Must show:

- route nodes and locked/available paths;
- current force condition;
- gold, momentum, and run score;
- one highlighted next decision;
- seed code and run number as quiet provenance;
- controller path and primary action.

Do not show every relic, unit stat, and future node at once. Details open in a side tray.

### B. Mission Brief

Purpose: give the player the minimum useful context before the table opens.

Must show:

- objective type and loss condition;
- opposition silhouette and known traits;
- weather and terrain modifiers;
- deployment budget and reserve limit;
- reward and risk;
- `Deploy force` as the only primary action.

### C. Planning — idle

Purpose: establish the battlefield, player force, objective, and available budget.

Layout:

- **Situation strip:** thin top rail for operation, objective, budget, reserve, and input hints.
- **Force tray:** collapsible left rail with compact unit plates.
- **Table:** dominant central world view.
- **Intel tab:** collapsed right-edge handle; opens Oracle and threat detail.
- **Commit bar:** bottom-center action, inactive until a valid force exists.

The table must remain the largest object. At 1280×800, planning UI may occupy no more than 38% of the frame and must not cover the active deployment zone.

### D. Planning — unit selected

Purpose: make one unit’s options obvious without losing the formation.

Must show:

- selected unit identity, role, health, cost, and core traits;
- clear placement cell and movement affordance;
- stance segmented control;
- target-priority control;
- conditional trigger, when available;
- a before/after indicator when the order changes the Oracle;
- controller glyphs that match the active input mode.

The order control should be anchored to the selected unit or force tray, not presented as a modal covering the table.

### E. Oracle comparison

Purpose: show whether the player improved the plan.

Must show:

- measured win/draw/loss rates plus the engine's named confidence band;
- prior plan vs current plan;
- unit survival bands;
- dominant enemy threat;
- median engagement length;
- confidence/sample provenance;
- one short human sentence: e.g. “Aggressive Standard-Bearer converts stalemates into wins, but remains sacrificial.”

The Oracle never reveals the reserved live seed’s result. It describes robustness, not destiny.

Recalculation contract:

- an order edit starts a short debounce, then a new 200-seed measurement;
- the prior plan remains visible, while the current column becomes `MEASURING 200 OUTCOMES…` rather than showing a stale number as current;
- each request carries a monotonically increasing presentation ID; a late result cannot overwrite a newer plan;
- `No measured change across 200 outcomes` is a valid result, not an error;
- unavailable data uses `FORECAST UNAVAILABLE · PLAN NOT MEASURED` and preserves the ability to commit after an explicit warning;
- Commit never silently implies that a stale forecast describes the current orders.

### F. Commit

Purpose: create a clear moment of consequence.

States:

- invalid plan;
- ready;
- confirm with unresolved warning;
- sealed;
- resolving.

The primary label is **Commit orders**, not a generic `FIGHT`. A short secondary line can state the commitment: “3 units · 1 custom order · 771/1500 pts.”

### G. Combat

Purpose: let the miniatures and causality read.

UI behavior:

- force and Intel rails retract;
- objective strip remains, reduced;
- friendly/enemy force condition is compact and symmetric;
- speed, pause, and camera controls sit in a small controller-aware cluster;
- kill feed shows only consequential events;
- nameplates use role and allegiance by shape as well as color;
- damage numbers are restrained and never obscure unit silhouettes;
- no planning controls remain interactive.

Combat UI may occupy no more than 20% of the frame.

### H. After Action

Purpose: make the result understandable and make the next decision tempting.

Must show:

- result and objective outcome;
- one-sentence cause;
- casualties and survivors;
- MVP/load-bearing unit;
- the plan’s predicted band vs actual result, explicitly labeled as one outcome;
- three to five decisive timeline events;
- rewards, momentum, and persistent injuries;
- `Continue to map` as the primary action;
- `Inspect replay` as secondary.

### I. Reward / recruit / shop draft

Purpose: reuse the same choice grammar across the roguelike.

Three offers maximum in the initial composition. Each choice shows its real tradeoff and how it changes the force, not just rarity color.

### Vision boundary

The Figma prototype deliberately shows the whole expedition loop because the interaction system needs a destination. The first Unity proof slice is narrower: a selected mission enters Planning, passes through Commit and Combat, and ends in After Action. `RunMap`, recruit drafts, relics, shops, and other run-state models exist in `WarTable.Game`, but the audited Unity runtime does not present that expedition state.

Therefore:

- Run Map and recruit/reward frames are approved north-star designs in Sprint A;
- the first Unity slice may use a committed fixture or adapter-backed selected mission;
- a later `RunStatePresenter` connects the full map and reward loop only after the core shell, input, and Oracle bridge pass parity;
- no Figma-only route choice is described as playable until that presenter exists.

---

## 7. Figma file architecture

Create one file named **War Table — Commander’s Table**.

### Pages

1. `00 / Cover + thesis`
2. `01 / Current-state audit`
3. `02 / Direction studies`
4. `03 / Foundations`
5. `04 / Components`
6. `05 / Expedition flow — desktop`
7. `06 / Expedition flow — Steam Deck`
8. `07 / Ultrawide + edge cases`
9. `08 / Prototype + motion`
10. `09 / Accessibility + input`
11. `10 / Dev handoff`

### Required finished frames

| Frame | Desktop | Steam Deck | Ultrawide | Prototype state |
|---|---:|---:|---:|---|
| Run Map | 1920×1080 | 1280×800 | 3440×1440 | route selected |
| Mission Brief | 1920×1080 | 1280×800 | — | ready to deploy |
| Planning / idle | 1920×1080 | 1280×800 | 3440×1440 | valid force |
| Planning / unit selected | 1920×1080 | 1280×800 | — | Standard-Bearer selected |
| Planning / Oracle compare | 1920×1080 | 1280×800 | — | stance changed |
| Commit warning | 1920×1080 | 1280×800 | — | unresolved risk |
| Combat | 1920×1080 | 1280×800 | 3440×1440 | mid-resolution |
| After Action | 1920×1080 | 1280×800 | — | player victory |
| Recruit choice | 1920×1080 | 1280×800 | — | one choice focused |
| Settings / accessibility | 1920×1080 | 1280×800 | — | text scale 125% |

### Required edge-state frames

These live on `07 / Ultrawide + edge cases` or `09 / Accessibility + input`; they are not optional polish:

| State | Required proof |
|---|---|
| Empty deployment | 0 units, 0/1500 points, Commit disabled, one clear next action |
| Unaffordable unit | Cost and remaining points explain why placement is unavailable without relying on red |
| Maximum force / reserve | Eight fielded units plus reserve remain scannable without turning the bottom edge into a card wall |
| Oracle measuring | Prior remains readable; current is explicitly pending; old values are not mislabeled |
| Oracle no delta | Honest `No measured change` state with the changed order still visible |
| Oracle unavailable | Missing evidence language, retry action, and guarded Commit path |
| Draw After Action | Draw gets its own hierarchy and cause, not a recolored defeat screen |
| Deck at 150% text | No clipped objective, order, Oracle, or Commit copy; lower-priority data may collapse by rule |
| Ultrawide 3440×1440 | Interaction stays inside the 1920 px core while the world expands naturally |
| Input hot-swap | Focus and selected-unit state survive mouse/keyboard ↔ controller switching; glyphs update in place |
| Long-copy stress | Add 35% to operation, unit, and explanation strings without destructive overlap |

### Direction studies

The first design checkpoint must include three genuinely different compositions, not three palettes:

1. **Quiet Command** — edge rails, acetate Oracle, table-first. Recommended.
2. **Map Room** — more paper/map material and pinned decisions, less persistent telemetry.
3. **Instrument Panel** — denser metal/engraved controls, fastest scan, least atmospheric.

Only one direction advances after a direct Kruz review.

For an honest comparison, all three studies use the same `Obsidian Relay` fixture, the same `play_wide` world crop, the same selected Standard-Bearer, the same 771/1500-point state, and the same Oracle values. Each direction supplies:

- one 1920×1080 Planning / selected-unit frame;
- one 1920×1080 Oracle-open state;
- one rough 1280×800 Deck adaptation;
- one five-second grayscale/blur test showing hierarchy without color or readable copy.

Judge the gate on five questions, in this order:

1. Does the table remain the main character?
2. Can Kruz find the selected unit, current order, biggest threat, and Commit action in five seconds?
3. Does it feel like a game-specific command instrument rather than a styled web dashboard?
4. Does the composition remain desirable at Deck size and comfortable on ultrawide?
5. Is there one signature move worth remembering—the Oracle acetate, map pin, or instrument interlock?

Do not polish all three. Spend the detail budget only after this gate.

---

## 8. Figma variables and modes

Use aliased variables rather than directly bound hex values.

### Collections

#### `WT / Primitives`

Raw color, spacing, radius, stroke, duration, and opacity values.

#### `WT / Semantic`

Surface, text, border, action, friendly, hostile, warning, success, focus, disabled, and world-overlay roles.

#### `WT / Layout`

Modes:

- `Desktop / 1920`
- `Deck / 1280`
- `Ultrawide / 3440`

Controls margins, rail widths, gaps, target sizes, typography steps, and table-safe zones.

#### `WT / Accessibility`

Modes:

- `Default`
- `High contrast`
- `Deuteranopia safe`
- `Protanopia safe`
- `Tritanopia safe`
- `Shapes only`

The deficiency-named modes do not communicate allegiance through a simulated palette alone. They require the existing friendly chevron / hostile diamond-plus-X shape grammar, a text label where space permits, and pattern differences on forecast and objective fills. `Shapes only` removes semantic hue from allegiance marks entirely while retaining neutral focus and warning colors.

High-contrast semantic overrides:

| Role | Value | Contrast on `#0A0E0C` |
|---|---|---:|
| surface | `#0A0E0C` | — |
| primary text | `#FFF8E8` | 18.36:1 |
| secondary text | `#DED5C4` | 13.34:1 |
| strong border | `#73837B` | 4.87:1 |
| friendly | `#7FC6BB` | 9.90:1 |
| hostile | `#F08A7D` | 7.99:1 |
| warning | `#F3B968` | 11.05:1 |
| focus | `#FFE08A` | 15.06:1 |

Every color-vision mode must be reviewed in grayscale and with allegiance labels hidden one at a time. If removing either color or text makes a critical state ambiguous, the shape/pattern grammar is incomplete.

#### `WT / Prototype state`

Variables:

- `phase`: map / brief / planning / sealed / combat / after-action
- `selected-unit`: none / vanguard-rifleman / standard-bearer / bulwark-mbt
- `stance`: balanced / aggressive / defensive / guard / hold-fire
- `priority`: nearest / lowest-hp / focus-highest-attack / finish-wounded
- `oracle-prior-wins` / `draws` / `losses`: numeric
- `oracle-current-wins` / `draws` / `losses`: numeric
- `oracle-prior-confidence` / `oracle-current-confidence`: string
- `oracle-status`: idle / measuring / ready / no-delta / unavailable
- `intel-open`: boolean
- `input-mode`: mouse-keyboard / xbox / steam-deck
- `text-scale`: 100 / 125 / 150
- `reduced-motion`: boolean

Use prototype variables to make the hero flow interactive instead of duplicating dozens of disconnected frames.

Plan fit: the largest collection in this file is `WT / Accessibility` at six modes. Figma Professional currently allows up to ten modes per collection, so this architecture fits the upgraded plan without splitting one semantic system into artificial collections. Keep the ceiling visible in the Dev Handoff page; a seventh through tenth mode needs a named product reason, not token sprawl.

---

## 9. Visual foundations

### Palette

| Token | Value | Use |
|---|---|---|
| `ink/950` | `#090B0A` | world void / deepest surface |
| `gunmetal/900` | `#121715` | primary UI plate |
| `gunmetal/800` | `#1B221F` | raised control |
| `steel/600` | `#5A6862` | strong border / disabled structure |
| `felt/800` | `#1D3A31` | table material |
| `felt/650` | `#315448` | deployment zone |
| `bone/100` | `#DED7C9` | primary text |
| `bone/300` | `#B6AEA0` | secondary text |
| `brass/500` | `#A98955` | selected / commitment / hardware |
| `friendly/500` | `#639A91` | player state |
| `hostile/500` | `#BF6B63` | enemy state |
| `warning/500` | `#C38C45` | risk / unresolved condition |
| `focus/400` | `#E3C77D` | keyboard/controller focus |

Rules:

- brass is a decision accent, not a page background;
- friendly/hostile colors always pair with shape, label, or pattern;
- normal-size semantic text on `gunmetal/900` must remain at or above 4.5:1; structural borders and focus geometry must remain at or above 3:1;
- combat effects must be tuned against the table, never pure emissive white;
- no large high-opacity black scrims unless the game is paused.

### Typography

Recommended starting stack, using freely available families that can ship predictably:

- **Barlow Condensed** — operation titles, round labels, large result language;
- **IBM Plex Sans** — controls, explanations, unit and objective copy;
- **IBM Plex Mono** — seed, forecast provenance, points, timing, and measured values.

Because typography has been a hard rejection point in prior design reviews, the stack is not considered approved from a text spec alone. Before building components, create one type-proof frame with identical layout, color, size, and copy across three trials:

1. **Barlow Condensed + IBM Plex Sans + IBM Plex Mono** — recommended; most command-instrument character.
2. **Archivo Narrow + Source Sans 3 + IBM Plex Mono** — calmer and more editorial.
3. **IBM Plex Sans + IBM Plex Mono only** — most restrained; no display-face affect.

The specimen must include `OBSIDIAN RELAY`, `Standard-Bearer`, `771 / 1500 PTS`, `74% · FAVORED`, a two-line mission description, and the Deck-size 12 px telemetry floor at 100% and 125% scale. Reject any trial that feels like esports branding, a SaaS dashboard, or compressed military cosplay. Kruz selects the type system at the same checkpoint as the composition direction.

Steam Deck floor:

- body: 15–16 px equivalent;
- secondary: 13–14 px;
- telemetry: 12 px minimum;
- primary action: 16–18 px;
- operation title: 26–32 px.

No essential text may rely on Valve’s 9 px absolute minimum. The target is 12 px or larger at 1280×800, with 125% and 150% text-scale modes.

### Geometry

- corners: 2 / 4 / 8; no soft 16–24 px SaaS cards;
- default stroke: 1 px; selected: 2 px with an external focus indicator;
- shadows: low, broad world separation only;
- texture: felt weave, powder-coat grain, paper tooth at 2–4% opacity;
- spacing base: 4 px; primary rhythm: 8 / 12 / 16 / 24 / 32.

### Layout grids

- Desktop: 12 columns, 64 px margin, 24 px gutter.
- Steam Deck: 8 columns, 24 px margin, 12 px gutter.
- Ultrawide: fixed-width edge rails; center table expands; critical controls remain within a 1920 px interaction core.

---

## 10. Component system

### Core components

1. `SituationStrip`
2. `ObjectivePlate`
3. `ResourceCounter`
4. `ForceTray`
5. `UnitPlate`
6. `UnitNameplate`
7. `TraitMark`
8. `RoleGlyph`
9. `OrderSheet`
10. `StanceControl`
11. `TargetPriorityControl`
12. `ConditionalTriggerRow`
13. `IntelHandle`
14. `OraclePanel`
15. `ForecastReadout`
16. `SurvivalRow`
17. `ThreatRow`
18. `PlanDelta`
19. `CommitControl`
20. `ControllerHint`
21. `FocusFrame`
22. `CombatConditionBar`
23. `ObjectiveProgress`
24. `CombatSpeedControl`
25. `ConsequentialFeedRow`
26. `AfterActionHeader`
27. `CasualtyRow`
28. `TimelineEvent`
29. `RewardChoice`
30. `RouteNode`
31. `RelicMark`
32. `SettingsRow`
33. `Toast`
34. `ConfirmSheet`

### Required variants

- phase: planning / combat / after-action;
- state: idle / hover / focused / selected / disabled / warning / resolved;
- input: mouse-keyboard / Xbox / Steam Deck;
- density: desktop / Deck;
- contrast: default / high;
- unit allegiance: friendly / hostile / neutral;
- evidence state: measured / estimated / unavailable.

Every interactive component needs visible focus, disabled, selected, and controller-focused variants before the polished screens are considered finished.

---

## 11. Hero prototype script

Use real War Table vocabulary and plausible fixture values.

1. Start on Run 07, node `Obsidian Relay`.
2. Select an Annihilate engagement on the sample tactical grid, with its central wall and split deployment.
3. Open the Mission Brief; opposition is `Spectre Jammer`, `Hunter-Killer Drone`, and `Adamant Bulwark` from the real `EnemyArchetypes` catalog.
4. Deploy Vanguard Rifleman, Standard-Bearer, and Bulwark MBT from `ShowcaseScenario.ReferencePlayer()`.
5. Select Standard-Bearer. Its resolved auto stance is `Defensive`.
6. Change stance to `Aggressive`; leave target priority inherited at `Lowest HP` so the measured delta has one cause.
7. Open Oracle:
   - prior plan: 108 W · 92 D · 0 L · 54% win rate · `Coin flip` · n=200;
   - current plan: 148 W · 52 D · 0 L · 74% win rate · `Favored` · n=200;
   - the order converts 40 sampled draws into wins without introducing a sampled loss;
   - Standard-Bearer remains `Critical` at 0% survival in both plans;
   - Bulwark MBT survival improves from 54% to 74%;
   - `Adamant Bulwark` remains the dominant threat, falling from 1.46 to 1.26 player kills per sampled battle;
   - median winning battle length remains 14 ticks.
8. Commit orders with 771/1500 points and 1 custom order.
9. Resolve combat with the UI retracted.
10. Show the reserved seed-7 result: player victory on tick 14; Standard-Bearer fell on tick 5; Bulwark MBT survived at 22 HP. Label it as one outcome, not proof of certainty.
11. Continue to a three-choice recruit draft.

The prototype must support backtracking from Commit to Planning without resetting the plan.

### Fixture copy deck

Use real vocabulary and finished microcopy from the first frame. No lorem ipsum, generic `Option 1`, or invented order axes.

| Surface | Primary copy | Supporting copy |
|---|---|---|
| Mission objective | `ANNIHILATE` | `Break every hostile unit before the battle closes.` |
| Deployment points | `771 / 1500 PTS` | `3 units fielded` |
| Standard-Bearer stance: before | `DEFENSIVE · AUTO` | `Cautious infantry posture. Lower outgoing and incoming damage.` |
| Standard-Bearer stance: after | `AGGRESSIVE · ORDERED` | `Higher outgoing and incoming damage.` |
| Target priority | `LOWEST HP · INHERITED` | `No per-unit targeting override in this comparison.` |
| Conditional trigger | `IF HP FALLS BELOW 30%` | `Switch stance or target priority once.` |
| Oracle title | `PLAN ROBUSTNESS` | `200 deterministic outcomes · live seed excluded` |
| Oracle prior | `54% · COIN FLIP` | `108 W · 92 D · 0 L · sample 200` |
| Oracle current | `74% · FAVORED` | `148 W · 52 D · 0 L · sample 200` |
| Oracle explanation | `40 stalemates become wins. Standard-Bearer remains sacrificial.` | `Adamant Bulwark remains the dominant threat` |
| Commit | `COMMIT ORDERS` | `3 units · 1 custom order · 771/1500 pts` |
| Commit warning | `1 MATERIAL RISK REMAINS` | `Review exposed units, or hold to commit.` |
| After Action result | `VICTORY · TICK 14` | `Bulwark MBT removed all three hostiles; Standard-Bearer fell on tick 5.` |
| After Action forecast | `ONE OUTCOME` | `Victory · forecast was 74% Favored, not certain` |
| After Action next step | `CONTINUE TO MAP` | `Inspect replay` |

The seed-7 After Action timeline is also fixture truth:

| Tick | Event |
|---:|---|
| 1 | Bulwark MBT destroys Hunter-Killer Drone. |
| 5 | Adamant Bulwark destroys Standard-Bearer. |
| 10 | Bulwark MBT destroys Adamant Bulwark. |
| 14 | Bulwark MBT destroys Spectre Jammer; victory. |

Bulwark MBT is the seed-7 MVP with three kills and 22 HP remaining. Vanguard Rifleman survives at 100 HP. These values give the After Action composition real asymmetry: a clean result headline, a costly sacrifice, a load-bearing survivor, and a forecast that was favorable but not certain.

These values were measured on `cycle6/integrate` commit `5a0bb2c`: `Terrain.Sample()`, Annihilate, stances enabled, all other combat toggles default, seeds 1–201 with live seed 7 excluded, and exactly one order changing Standard-Bearer from its auto `Defensive` stance to `Aggressive`. At implementation kickoff, rerun the fixture and update the counts, rates, confidence label, unit survival, threats, median ticks, and summary together; never mix numbers from separate Oracle runs.

---

## 12. Motion and sound handoff

Figma demonstrates timing and sequence; Unity owns final feel.

### Motion language

- 120 ms: focus, hover, glyph swap;
- 180 ms: tray open/close, selected-state change;
- 260 ms: Oracle acetate slide, phase transition;
- 420 ms: Commit seal and combat UI retract;
- no springy card motion;
- no scale-pop on every update;
- forecast values crossfade and slide by one text line, not count-up theatrics.

### Phase transition

On Commit:

1. warning states resolve;
2. Commit control compresses into a sealed operation strip;
3. Oracle and force rails retract;
4. table lighting tightens;
5. combat objective strip remains.

Reduced-motion mode performs an immediate visibility swap with a 100 ms opacity transition.

### Implementation sound intent

- focus: dry mechanical tick;
- valid order: quiet metal detent;
- warning: muted double tap;
- commit: low latch and paper/acetate movement;
- phase start: restrained radio cue;
- no constant UI chirps.

---

## 13. Accessibility and Steam Deck contract

Valve’s current Deck compatibility criteria require full physical-control access, correct active-input glyphs, 1280×800 support, and readable text at a 12-inch viewing distance. Their absolute minimum character height is 9 px, with 12 px recommended. This spec treats 12 px as a floor for telemetry and uses larger sizes for normal reading.

Required:

- every action reachable with controller only;
- deterministic focus order shown in Figma;
- active input glyphs switch between mouse/keyboard, Xbox, and Deck;
- 48×48 px minimum Deck target;
- no information communicated by hue alone;
- text scale at 100%, 125%, and 150%;
- reduced motion;
- high contrast;
- deuteranopia/protanopia modes;
- pause and speed controls during deterministic replay;
- no gameplay-critical text over active visual effects;
- safe-zone test at both 1280×800 and 1280×720.

### Controller grammar

Controller interaction is spatial and phase-aware; it must not be a fake mouse cursor.

| Context | Input | Result |
|---|---|---|
| Any UI | D-pad / left stick | Move deterministic focus |
| Planning table | D-pad / left stick | Move a high-contrast grid cursor one cell at a time |
| Planning table | `A` | Pick up / place / confirm selected unit |
| Planning table | `B` | Cancel move, close sheet, or step back |
| Planning table | `LB` / `RB` | Cycle fielded units |
| Selected unit | `X` | Open the anchored Order Sheet |
| Planning | `Y` | Toggle Oracle acetate; focus moves into it only when explicitly opened |
| World camera | Right stick | Orbit; triggers zoom; stick press resets the approved view |
| Focused Commit control | hold `A` for 450 ms | Seal orders after warnings are reviewed |
| Combat | `X` / `Y` | Pause-resume / cycle speed |
| Any phase | Menu | Pause and settings |

Every Figma screen shows only the three or four actions valid in that phase. The focus path must survive a mouse-to-controller switch without losing the selected unit or opening a control unexpectedly.

---

## 14. Implementation handoff

### Architecture recommendation

Do not rewrite the simulation or world presentation. Introduce the new interface as a feature-flagged shell.

War Table is on Unity `6000.0.77f1`. Unity’s current recommendation for new UI work is UI Toolkit, which provides UXML structure, USS styling, Flexbox-based responsive layout, and runtime data binding. The existing game can phase this in without deleting uGUI first.

Recommended structure:

```text
unity/Assets/WarTable/UI/
  Tokens/
    WarTableTokens.uss
    WarTableHighContrast.uss
  Components/
    UnitPlate.uxml
    OraclePanel.uxml
    CommitControl.uxml
    AfterActionRow.uxml
  Screens/
    PlanningHud.uxml
    CombatHud.uxml
    AfterAction.uxml
  Presenters/
    PlanningHudPresenter.cs
    CombatHudPresenter.cs
    AfterActionPresenter.cs
  ViewModels/
    OracleViewModel.cs
  Adapters/
    DeployStateAdapter.cs
    OracleStateAdapter.cs
    BattleStateAdapter.cs
    AfterActionTimelineAdapter.cs
  Input/
    InputGlyphService.cs
```

### Shared planning/forecast assembly is a prerequisite

The repository boundary is stricter than the product boundary. The audited Unity project currently vendors only `WarTable.Sim.dll` and `WarTable.Content.dll` under `unity/Assets/Plugins/WarTable`. `WarTable.Game.csproj` targets `net9.0` only, so Unity cannot load the existing `UnitOrder`, `BattleComposer`, or `PreviewOracle` types even though they are the correct gameplay source.

Do not copy those classes into `unity/Assets`. A diagnostic build that forced the entire Game project to `netstandard2.1` with the latest C# language version failed with **741 compatibility errors**: missing record/required-member support types, `System.Text.Json` APIs, immutable collections, and other Game-wide dependencies. A normal restore and `net9.0` build was immediately rerun and passed with zero warnings and zero errors. The whole Game assembly is therefore the wrong Unity seam.

Create a narrow shared project instead:

```text
src/WarTable.Planning/
  WarTable.Planning.csproj       # net9.0;netstandard2.1
  UnitOrder.cs
  BattleComposer.cs              # includes StancePolicy
  PlanningOracle.cs              # deterministic seed-sweep core
  ForecastModels.cs              # ForceForecast, UnitOutlook, ThreatRead, bands
```

The extraction contract is:

1. `WarTable.Planning` references only `WarTable.Sim` and `WarTable.Content`;
2. move the existing order/composition implementation rather than cloning it;
3. keep the current namespace/API shape where practical during the first extraction so the heavily tested Game path does not churn merely for naming;
4. keep recorded-run adaptation in `WarTable.Game`: its `PreviewOracle` facade may translate a `RunStep` and forward to the one shared seed-sweep core;
5. make `WarTable.Game` reference `WarTable.Planning`, and vendor `WarTable.Planning.dll` beside the Sim and Content DLLs for Unity;
6. prove that identical fixture, order, toggles, terrain, seed set, and excluded live seed produce an identical `ForceForecast` through the Game facade and Unity-facing shared call;
7. forbid a Unity-local copy of `UnitOrder`, composition rules, forecast models, confidence bands, or Oracle math.

This seam is an extraction spike, not permission to redesign the engine. If the exact move reveals an unexpected Game-only dependency, reduce the shared API to a plain planning request/forecast result boundary and keep the implementation centralized; never solve it with two implementations.

### Input foundation is a prerequisite

The audited Unity project does not currently include `com.unity.inputsystem`. Its planning and camera interactions call the legacy `Input` API directly, and its uGUI canvases create `StandaloneInputModule` instances. Therefore, “Steam Deck ready” is new implementation work, not a visual QA checkbox.

Before the new shell can pass the Deck gate:

1. install and pin the Unity Input System package compatible with Unity `6000.0.77f1`;
2. create `WarTableActions.inputactions` with `UI`, `PlanningTable`, `Camera`, and `Combat` action maps;
3. migrate the first-slice `DeployController` placement and `OrbitCamera` paths away from raw `Input.*` calls;
4. use exactly one active UI event module for the feature-flagged scene, matching Unity's active input backend;
5. have `InputGlyphService` expose the last active control scheme without changing game state;
6. prove mouse/keyboard, Xbox-style gamepad, and Steam Deck navigation independently.

Unity 6 supports UI Toolkit events from either input backend, but the modern Input System is the better-supported device path. During the uGUI/UI Toolkit overlap, follow Unity's module matrix: the mixed scene uses an `EventSystem` with the module matching the active backend. Never leave both UI modules processing the same submit/click path.

### Existing seams to preserve

| Existing source | New responsibility |
|---|---|
| `DeployController.cs` | authoritative placement and fight action |
| `DeployHud.cs` | retained as fallback until planning parity |
| `BattleHud.cs` | retained as fallback until combat parity |
| `ResultsScreen.cs` | authoritative result data, later replaced visually |
| `ArenaController.cs` | Arena state; no redesign in first implementation slice |
| `ArenaHud.cs` | later consumer of shared components |
| Shared `PlanningOracle` core, surfaced through the existing Game-level `PreviewOracle` facade | authoritative forecast; no UI-side recomputation or copied seed sweep |
| `BattleStats` | authoritative per-unit kills, damage, survival, MVP, and side totals |
| `ReplayLog` | authoritative tick/event stream for a derived decisive-event timeline |
| `GameSettings` | keep current audio, slow-motion, table-lighting, grade, and color-vision settings; extend additively |

### Data contract: repository truth vs Unity presentation work

The design must distinguish what the repository exposes today from what Unity can load today and what the new shell has to derive or persist. Nothing in the right column may add combat math, RNG, or replay facts. Authored `UnitOrder` values are allowed to change an outcome through the already-tested engine path—that is their purpose—but the UI may not recompute or mutate simulation truth.

| Surface need | Repository source available now | Shared-boundary, presentation, or settings work required |
|---|---|---|
| Per-unit orders | `UnitOrder`: `Balanced`, `Aggressive`, `Defensive`, `Guard`, `HoldFire`; priorities `Nearest`, `LowestHp`, `FocusHighestAttack`, `FinishWounded`; triggers `HpBelow30`, `AlliesBelow2` | Extract the existing type/composition path into `WarTable.Planning`; then add human-readable labels and controller-safe controls in the Unity presenter |
| Oracle headline | `ForceForecast`: sample, wins, draws, losses, calculated rates, and `Doomed` / `Unfavored` / `CoinFlip` / `Favored` / `Dominant` | Extract the deterministic seed-sweep core and forecast models once; `OracleViewModel` only formats measured rates and named confidence. Do not invent statistical intervals |
| Oracle unit/threat detail | `UnitOutlook`: survival/fall rate, `Solid` / `Exposed` / `Critical`, top killer; `ThreatRead`: kills per battle; median win ticks | Consume the shared forecast; `OracleStateAdapter` maps it to rows and a short deterministic summary sentence |
| Prior vs current plan | The Oracle can calculate either plan independently | Snapshot the last completed forecast in the presenter so the UI can compare prior and current plans; cancel stale async results |
| Basic After Action | `BattleStats`: unit name, kills, damage, survived, MVP; result and side totals | Map directly into the new recap without recomputation |
| Decisive timeline | `ReplayLog.Ticks` with attack, damage, death, heal, suppress, reveal, jam, move, cover, rout, cast, and trigger events | `AfterActionTimelineAdapter` ranks three to five consequential events with deterministic rules; it does not create new combat facts |
| Result cause and objective outcome | Final result, replay header objective, final units, and tick log | Add a deterministic cause-summary adapter and objective-specific copy rules; expose `Unavailable` when evidence is insufficient |
| Expedition map and reward loop | `RunMap`, `RunEngine`, recruit draft, shop, relic, event, and run-state models exist in `WarTable.Game` | No expedition presenter exists in the audited Unity runtime; keep these frames as north-star design until a later `RunStatePresenter` slice |
| Accessibility | Color-vision/shape modes and kill slow-motion already exist | Add `TextScale`, `ReducedMotion`, and `HighContrast` to `GameSettings`; add UI bindings and persistence without changing current defaults |
| Input and glyphs | The audited shell is legacy mouse input: raw `Input.*`, `StandaloneInputModule`, and no Input System package | Add action maps and controller navigation first; `InputGlyphService` then observes the active control scheme and maps actions to mouse/keyboard, Xbox, and Deck glyph sets |

The current Unity runtime does **not** load `WarTable.Game.dll`, so this is more than missing UI wiring: it cannot directly reference `PreviewOracle`, `UnitOrder`, or `BattleComposer`. The shared planning/forecast assembly must pass parity before `DeployController` or the new presenter binds those models. Treat that bridge as a first-slice dependency, not as finished UI state.

Keep `CommanderTableUiV1` presentation-only. A separate battle configuration enables the already-existing stances/order path for both the new shell and the uGUI fallback. With identical config and `UnitOrder` input, both surfaces must compose the same battle; switching the UI flag alone must never change an outcome.

### Migration rule

Land one vertical slice behind `CommanderTableUiV1`:

1. planning;
2. combat transition;
3. After Action.

Keep the old canvases available until the new slice passes input, rendering, and state-parity tests. Do not attempt a simultaneous Arena, settings, codex, kill-feed, and expedition rewrite.

### Token handoff

Every Figma semantic variable gets:

- an exact USS custom-property name;
- a description;
- a mode/value table;
- a Figma node reference;
- a Unity implementation status.

No hard-coded color should be introduced in a new screen presenter.

---

## 15. Sprint plan

### Sprint A — Figma design only (3.5–4.5 focused hours)

1. **Audit and three compositions** — current-state annotations and three genuinely different directions.
2. **Direction gate** — Kruz selects one before high-fidelity expansion.
3. **Foundations** — variables, type, grids, materials, input glyph grammar.
4. **Hero planning flow** — deployment, orders, Oracle, Commit.
5. **Combat + After Action** — phase reduction and learning surface.
6. **Deck + ultrawide** — rebuild layouts using modes, not scaled screenshots.
7. **Prototype** — complete Run Map → recruit-choice click path.
8. **QA and handoff** — accessibility, overflow, variable binding, node ledger, implementation map.

### Sprint B-1 — shared planning/forecast seam (2–4 focused hours)

- create the multi-target `WarTable.Planning` project with only Sim and Content dependencies;
- move, do not duplicate, the minimum order/composition/forecast code;
- retain a thin Game-level recorded-run adapter/facade;
- add the shared DLL to Unity's existing plugin-sync path;
- prove the `Obsidian Relay` fixture is identical through Game and Unity-facing entry points;
- run the full .NET suite and a Unity editor compile smoke test before UI work.

### Sprint B0 — input/controller foundation (2–3 focused hours)

- pin the Input System package;
- author the four action maps;
- migrate first-slice placement and camera controls;
- one event module and one focus owner;
- controller/grid-cursor proof before visual migration.

### Sprint B1 — Unity vertical slice (4–6 focused hours)

- token foundation;
- feature-flagged UI Toolkit shell;
- Planning → Commit → Combat → After Action;
- controller focus and active glyphs;
- headless 1920×1080 and 1280×800 proof renders;
- no new mechanics.

### Sprint C — feel and proof (90–120 minutes)

- real play path;
- typography and table-safe-zone tuning;
- animation and audio timing;
- Steam Deck controller pass;
- screenshot comparison against Figma;
- retire old HUD only after parity.

---

## 16. Acceptance gates

### Figma gate

- three direction studies reviewed before polish;
- three controlled type trials reviewed with real fixture copy;
- all required frames present;
- end-to-end prototype works;
- no missing fonts;
- no text overflow or clipped layers;
- no essential unbound colors, spacing values, or typography;
- all components use variants rather than detached copies;
- desktop, Deck, and ultrawide modes verified;
- default and high-contrast modes verified;
- controller focus path documented;
- exact node/token ledger committed beside this spec;
- Kruz explicitly approves the chosen direction.

### Unity gate

- no simulation-source or determinism-contract changes;
- Unity loads the shared planning DLL; no planning or Oracle implementation is copied under `unity/Assets`;
- Game facade and Unity-facing shared entry point return field-for-field identical forecasts for the committed fixture;
- switching `CommanderTableUiV1` alone produces the same battle setup and outcome;
- outcome changes from an authored order are accepted only when the same `UnitOrder` produces the same change through the non-UI Game-layer path;
- existing test suite remains green;
- state parity for deployment budget, selected unit, orders, Oracle, battle state, and result;
- 1280×800 and 1920×1080 headless captures;
- zero critical overlap with the table or deployment zone;
- all content controller reachable;
- correct active glyphs;
- no essential text below 12 px at 1280×800;
- no critical state encoded by color alone;
- old HUD remains behind the rollback flag until proof passes.

### Player proof

From a cold screenshot, a player should identify within five seconds:

1. the objective;
2. their selected unit;
3. whether the plan is valid;
4. what action commits the plan;
5. the largest forecast risk.

During combat, the player should be able to name what caused the decisive swing without reading a post hoc wall of text.

---

## 17. Scope cuts

Not in this design sprint:

- new combat mechanics;
- multiplayer;
- Steam publishing or store setup;
- monetization;
- full 3D model replacement;
- texture production beyond small material studies;
- Arena-mode redesign beyond component reuse notes;
- Battle of the Day vertical-video composition;
- exhaustive codex or settings redesign;
- procedural map generation;
- public marketing site.

The sprint succeeds by making one real expedition loop exceptional.

---

## 18. Risks and mitigations

| Risk | Mitigation |
|---|---|
| Figma art does not survive a moving 3D camera | Use real War Table renders as frame backgrounds from the start; test wide and tight camera states. |
| UI Toolkit migration becomes the project | Feature flag one slice; preserve uGUI fallback; no all-screen rewrite. |
| Game-layer models are mistaken for Unity-ready APIs | Extract one `net9.0;netstandard2.1` planning/forecast assembly first. The forced whole-Game retarget produced 741 errors, so do not vendor or duplicate the Game layer. |
| UI covers too much of the miniature table | Hard table-occupancy gates: ≥62% planning, ≥80% combat. |
| Tactical density becomes tiny on Deck | Deck is a first-class mode; 12 px telemetry floor, 15 px body, 48 px targets. |
| “Deck support” is assumed from responsive frames | Treat controller input as a prerequisite lane: action maps, grid cursor, focus graph, correct event module, and real-device proof. |
| Oracle looks like false certainty | Show measured rates, named confidence bands, comparison, sample count, and the reserved-seed rule. |
| “Tactile” becomes decorative cosplay | Every material treatment must reinforce hierarchy or interaction. |
| Current feature branch complicates implementation | Keep this spec in `project-docs`; implement only after cycle 6 is integrated cleanly. |

---

## 19. Ranked alternatives

### Selection snapshot

Scores are out of 35 across leverage, compounding value, first proof, autonomy unlock, user/revenue path, taste, and differentiation, with rubric penalties applied for external gates, repeat work, or dashboard-without-action risk. The number is a decision aid, not an automatic winner.

| Candidate | Score | Decisive read |
|---|---:|---|
| Agent Economics Observatory | 30 | Highest leverage; public claim model and daily use loop are not ready yet. |
| **War Table — Commander’s Table** | **29** | Best strategic taste and largest current core-to-surface gap. |
| MTG Workstation — Magic Desk | 28 | Excellent player pull; a major visual overhaul just shipped. |
| BuildForge Creator Studio | 28 | Existing product makes proof fast; live-client launch gate remains. |
| FlipRadar Resale Desk | 28 | Direct money path; interface design is not the current bottleneck. |
| DraftScope Premium Companion | 27 | Strong local proof; first live calibration should shape the layout. |
| Iron Gauntlet Campaign Room | 26 | Great IP direction; less implementation-ready than War Table. |
| MYRIAD Interaction Theater | 26 | Differentiated training/product surface; weaker player pull. |
| BrawlScope Match Desk | 26 | Compounding personal meta; narrower than Magic Desk. |
| GambaTime Newsroom | 25 | Strong action loop; creative evidence quality is the real constraint. |

Orbit also scored well mechanically, but it was removed from the finalist list because its Figma/design lane has just shipped. Repeating that surface would be the wrong use of the next premium sprint.

- **Highest leverage:** Agent Economics Observatory.
- **Fastest proof:** BuildForge Creator Studio.
- **Biggest long-term bet:** War Table — Commander’s Table.
- **Lowest-risk grind:** DraftScope Premium Companion.

### 1. Agent Economics Observatory — biggest long-term platform

- **Becomes:** a publishable, interactive evidence product for TPVS, routing confidence, turn cost, and model-class recommendations.
- **Compounds:** every benchmark and fleet run adds to the research corpus.
- **First sprint:** design the State of the Ratio story, class comparison, confidence, caveat ledger, and “what should route where?” decision surface.
- **Proof:** one report can be understood without opening Markdown or JSON.
- **Risk:** no UI exists and much of the corpus is snapshot/stale; a polished dashboard could overstate confidence.
- **Why not first:** excellent platform bet, but less immediate personal pull and one step further from a stable user loop.

### 2. MTG Workstation — “Magic Desk” — best backup

- **Becomes:** one unified player surface across Meta Hub, DraftScope, BrawlScope, Deck Doctor, wildcards, and personal run history.
- **Compounds:** every Arena session and daily data refresh improves the personal and public layers.
- **First sprint:** a unified shell plus the live Draft/Brawl second-screen layouts.
- **Proof:** Kruz completes a draft and a Brawl match without switching between unrelated tools.
- **Risk:** the public MTG hub just received a major design overhaul; redesigning before live companion calibration could optimize the wrong workflow.
- **Why backup:** real player pull is excellent; wait for the first live DraftScope calibration and BrawlScope spin.

### 3. BuildForge Creator Studio

- **Becomes:** a premium build-authoring and proof workspace rather than a collection of generators, dashboards, guides, and overlays.
- **Compounds:** one design system can unify BuildForge, BuildShare, BuildProof, and BuildLive.
- **First sprint:** build intake → leveling path → annotation QA → `.build` proof → share.
- **Proof:** a creator can make and verify one build without reading the CLI docs.
- **Risk:** public launch remains gated on real client load proof; broad visual work can outrun that gate.

### 4. FlipRadar Resale Desk

- **Becomes:** a personal resale operating surface from item identity and comps through photo checklist, channel copy, price floor, and sale receipt.
- **Compounds:** every listed item improves comp quality and listing templates.
- **First sprint:** one guided “RAM kit to ready listing” flow.
- **Proof:** Kruz prepares a real listing from scan to final copy in one session.
- **Risk:** platform listing actions remain Kruz-only and the current product is primarily scripts/Markdown; Figma could be more polish than bottleneck.

### 5. DraftScope Premium Companion

- **Becomes:** the best local, evidence-grounded Arena draft second screen: pack recommendation, archetype lean, build helper, run history.
- **Compounds:** every real draft calibrates handlers and personal results.
- **First sprint:** Pack → Pick → Build → Run review, Deck-first and never focus-stealing.
- **Proof:** first live calibration draft completes with no unreadable or unrecognized state.
- **Risk:** should follow the real calibration draft, because event-shape and information-priority findings may change the composition.

### 6. Iron Gauntlet Campaign Room

- **Becomes:** a visually coherent HOI4-as-roguelike campaign with a strong between-run command layer.
- **Compounds:** every validated arc and modifier can reuse the campaign grammar.
- **First sprint:** run map, choice event, force state, and failure recap.
- **Proof:** one complete Second Rome arc reads as a game, not a validator.
- **Risk:** more content/engine seams are still gated than War Table’s already-playable Unity loop.

### 7. MYRIAD Interaction Theater

- **Becomes:** a showable, legible interaction-first AI product with before/after walkthroughs and hotspot proof.
- **Compounds:** each interaction pattern becomes a training and evaluation primitive.
- **First sprint:** one end-to-end encounter with an explicit system-state and consequence model.
- **Proof:** a stranger can complete the showcase without narration.
- **Risk:** weaker immediate user/revenue pull than a player-facing game.

### 8. BrawlScope Match Desk

- **Becomes:** a premium live matchup surface for public commander info, expected-vs-seen cards, draw odds, and personal matchup history.
- **Compounds:** every ladder match enriches the personal meta.
- **First sprint:** match start → reveal tracking → result.
- **Proof:** one real Brawl session is clearer without crossing the hidden-info/advice rail.
- **Risk:** narrower than the unified Magic Desk and still awaiting a live spin beyond the ground-truth fixture.

### 9. GambaTime Newsroom

- **Becomes:** an editorial command surface joining source receipt, claim, script, evidence cues, render QC, publish receipt, and 72-hour grade.
- **Compounds:** every edition trains quality and distribution decisions.
- **First sprint:** one EvidenceCut story from source to grade.
- **Proof:** the newsroom makes unsupported/generic output structurally difficult.
- **Risk:** the live system’s bottleneck is creative quality and source-grounded footage, not primarily visual interface design.

### 10. PC Bottleneck Diagnostic Lab

- **Becomes:** an evidence-led consumer diagnostic experience that turns hardware symptoms into inspectable recommendations.
- **Compounds:** every artifact and question improves the diagnostic corpus and SEO surface.
- **First sprint:** symptom intake → evidence → bottleneck judgment → upgrade path.
- **Proof:** one real machine produces a credible, shareable artifact without fearmongering.
- **Risk:** strong commercial path, but the next return likely comes from distribution and artifact proof before a full design system.

---

## 20. Decisive recommendation

Build the Figma file for **War Table — The Commander’s Table** next.

Start with three compositions and a hard direction gate. Then design one complete expedition loop, Steam Deck first, with the Oracle as the signature interaction. Do not write Unity implementation code until Kruz approves the Figma prototype.

**Backup:** MTG Workstation — Magic Desk, after the first real DraftScope calibration and next BrawlScope session produce workflow evidence.

**Long-term platform bet:** Agent Economics Observatory, once its data freshness and public-claim model are ready for a visual product.

---

## Sources used for platform constraints

- Valve, [Steam Deck and Steam Machine Compatibility Review](https://partner.steamgames.com/doc/steamhardware/compat)
- Unity, [UI Toolkit overview](https://docs.unity3d.com/2023.2/Documentation/Manual/UIElements.html)
- Unity, [Input in Unity 6](https://docs.unity3d.com/6000.0/Documentation/Manual/Input.html)
- Unity, [Runtime UI event system and input handling](https://docs.unity3d.com/6000.0/Documentation/Manual/UIE-Runtime-Event-System.html)
- Figma, [Guide to variables](https://help.figma.com/hc/en-us/articles/15339657135383-Guide-to-variables-in-Figma)
- Figma, [Modes for variables](https://help.figma.com/hc/en-us/articles/15343816063383-Modes-for-variables)
- Figma, [Plans and feature limits](https://help.figma.com/hc/en-us/articles/360040328273-Figma-plans-and-features)
- Google Fonts: [Barlow Condensed](https://fonts.google.com/specimen/Barlow%2BCondensed), [IBM Plex Sans](https://fonts.google.com/specimen/IBM%2BPlex%2BSans), [IBM Plex Mono](https://fonts.google.com/specimen/IBM%2BPlex%2BMono), [Archivo Narrow](https://fonts.google.com/specimen/Archivo%2BNarrow), [Source Sans 3](https://fonts.google.com/specimen/Source%2BSans%2B3)
