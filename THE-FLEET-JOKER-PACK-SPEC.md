# THE FLEET — Balatro Joker Pack Design Spec

*Designed 2026-07-05 by a cloud lane in the final burn window. Companion: [BALATRO-MOD-SCOUT.md](BALATRO-MOD-SCOUT.md) (the stack + GO verdict). Status: design only — hello-world verify-step comes before any code.*

**Mod key:** `thefleet` · **Prefix:** `j_fleet_` · **Stack:** Steamodded (SMODS) 1.0-beta, Lua
**Atlas:** one 4×2 sheet, 71×95px cells, shipped at 1x (`fleet_atlas.png`) and 2x (`fleet_atlas@2x.png` — 142×190 cells). Positions `pos = {x = 0..3, y = 0..1}` in build order below.
**Art bible (applies to all 8):** the fleet's zero-glow look. Near-black card fields (#0d0d0f range), amber as the *only* accent (#e8a33d range, used sparingly — lit pixels are earned), terminal-dense pixel style: bracket frames, tick marks, monospace glyph shapes, dithered dark grays. No neon, no bloom, no gradients. Every card should read like a status line on the Orbit cockpit.

**Rarity scale:** 1 = Common, 2 = Uncommon, 3 = Rare.

---

## The Cast

### 1. Greenlight — the decision gate

- **Rarity:** 2 (Uncommon) · **Cost:** $7
- **Flavor:** *"Work waits. That's the point."*
- **Mechanic:** When a Blind is selected, all Jokers to the right of Greenlight are **debuffed** — except one. Greenlight **approves** one Joker per Blind, rotating left-to-right through the queue each Blind. The approved Joker is active and **triggers twice** this Blind (one extra repetition — a retrigger is a true ×2 on whatever the joker does). If there are no Jokers to its right, Greenlight does nothing.
- **Balance / vanilla comp:** power comps are **Blueprint** (Rare, $10) and **Dusk** (Uncommon, $5). With one joker in the queue, Greenlight is a Blueprint you can't aim for $3 less; with three, each spends 2 of 3 Blinds idle. The debuff tax IS the placement economics — gated work runs harder, but it *waits*.
- **Hooks:** `context.setting_blind` (rotation, debuffs, approval), joker-retrigger check (`repetitions = 1` for the approved joker), `context.end_of_round` (clear). Debuffs cleared on sell.
- **blueprint_compat:** `false`
- **Sprite:** dark card split by a horizontal gate bar; queue of four dim ticks below, one passed above the bar lit amber; tiny `OK` prompt glyph.

### 2. Emissary — sent out, returns with results

- **Rarity:** 2 (Uncommon) · **Cost:** $6
- **Flavor:** *"Sent with a mandate. Returns with receipts."*
- **Mechanic:** When a **Small Blind** is selected (or skipped), Emissary **deploys**: debuffed, does nothing that Blind. At round end it **returns with results**: earn **$3** and permanently gain **×0.25 Mult**. While home (Big + Boss Blinds — always home for the Boss), gives its accumulated xMult.
- **Balance / vanilla comp:** scaling mirrors **Constellation** (×0.1/planet) and undercuts **Hologram** (×0.25/card): strict ×0.25/ante ≈ ×2.0 by ante 5, always present for the fight that matters, plus **Rocket**-grade drip ($3/ante). The dead slot every Small Blind pays for the guaranteed rhythm.
- **Hooks:** `context.setting_blind`, `context.skip_blind` (skipped Small still counts), `calc_dollar_bonus`, `context.joker_main`.
- **blueprint_compat:** `true` (copies current xMult while home)
- **Sprite:** bracket-frame doorway left edge, courier glyph mid-stride exiting, dotted amber return-path arcing back with a packet at the apex.

### 3. Stoker — burns unspent budget before reset

- **Rarity:** 1 (Common) · **Cost:** $5
- **Flavor:** *"Unspent budget doesn't roll over. It burns."*
- **Mechanic:** Each played hand **stokes +10 Chips per unused card slot** (3-card hand stokes +20; 5-card stokes 0). Gives accumulated charge as Chips when scoring. At Blind end the furnace resets — leftover charge **burns into money: $1 per full 40 Chips, max $3** (`calc_dollar_bonus`).
- **Balance / vanilla comp:** within-blind ramp at **Ride the Bus** cadence on the chips axis; pairs across a 4-hand blind average near **Banner** with the opposite lifestyle (Banner hoards discards; Stoker wants small hands). Burn-off floor under **Delayed Gratification**. Hard anti-synergy with 5-card builds is the identity — the **Half Joker** archetype's chips engine.
- **Hooks:** `context.before` (charge), `context.joker_main` (payout), `context.end_of_round` (reset), `calc_dollar_bonus`. `loc_vars` shows live charge.
- **blueprint_compat:** `true` (copies payout, not accumulation)
- **Sprite:** furnace grate as heavy dark block-bars; ember pixels in amber only in the gaps, denser at bottom; tiny gauge needle top-right.

### 4. Orbit — verified progress, anti-stale-trust

- **Rarity:** 3 (Rare) · **Cost:** $8
- **Flavor:** *"Stale trust is the deadliest failure."*
- **Mechanic:** **Retriggers all scored cards** — but only on **fresh** hands: hand type differs from the previous hand played. Same type twice in a row = **stale**, no verification pass. First hand of every round always counts as fresh.
- **Balance / vanilla comp:** priced against **Dusk** ($5, final hand only) and **Seltzer** ($6, 10 hands then dies). Orbit is permanent and can fire every hand *if* you alternate hand types — a real deckbuilding constraint mono-hand decks fail. First-hand rule guarantees a Dusk-grade floor.
- **Hooks:** `context.repetition` with `context.cardarea == G.play`, `context.before` (record `last_hand`), `context.setting_blind` (clear). `loc_vars` shows FRESH/STALE.
- **blueprint_compat:** `true` (strongest Blueprint target in the pack; deliberate)
- **Sprite:** thin amber orbital ring around a dark core; a checkmark tick where the sweep line touches the ring; faint gray graticule dots.

### 5. REM — weekly consolidation: prune + strengthen

- **Rarity:** 3 (Rare) · **Cost:** $8
- **Flavor:** *"Every week, something gets pruned. Try not to be it."*
- **Mechanic:** When the **Boss Blind is defeated** (end of ante = end of week), REM **consolidates**: destroys your **lowest sell-value** other Joker, permanently gains **×0.5 Mult**, and Jokers **adjacent to REM gain +$2 sell value**. Prune priority: non-Fleet first; Eternals skipped; ties → rightmost. If REM is your only Joker: nothing (no food, no growth).
- **Balance / vanilla comp:** the sacrifice family — **Madness** (×0.5 per Small/Big but random destruction), **Ceremonial Dagger**, **Campfire** (resets each Boss). REM is the controlled version: ×0.5/ante permanent, one telegraphed casualty you can steer with $1-2 shop junk as prune-food. The sell-value rider is **Gift Card**-grade and doubles as armor — consolidation protects what it has invested in.
- **Hooks:** `context.end_of_round` gated on `G.GAME.blind.boss` (victim, `start_dissolve`, gains), `context.joker_main`. `loc_vars` shows xMult + marked victim during Boss.
- **blueprint_compat:** `true` (xMult only)
- **Sprite:** node-graph of five dim nodes; pruning-shears glyph closing on one half-dissolved node; two center nodes thickened with amber outlines; crescent tick top-left.

### 6. Hermes — the message broker

- **Rarity:** 3 (Rare) · **Cost:** $9
- **Flavor:** *"Doesn't do the work. Makes sure it arrives."*
- **Mechanic:** **Copies the ability of an adjacent Joker, alternating sides each hand played** — left on odd hands, right on even (arrow badge shows the live side). Incompatible targets show "Incompatible!" like Blueprint.
- **Balance / vanilla comp:** against **Blueprint** ($10, copies right, full control) and **Brainstorm** ($10, leftmost). Hermes trades $1 and control for coverage: you must build a *sandwich* where both neighbors are worth relaying. Worse than Blueprint with one great joker; better with two condition-gated jokers whose windows alternate.
- **Hooks:** standard SMODS copy pattern — forwards contexts to the live-side neighbor blueprint-style; side flips in `context.after`, resets left on `context.setting_blind`.
- **blueprint_compat:** `true` (chains resolve in board order)
- **Sprite:** two large monospace angle brackets at the card edges; amber packet square mid-flight between them with a dotted trail; alternating arrowheads above.

### 7. Atlas — the knowledge graph

- **Rarity:** 1 (Common) · **Cost:** $5
- **Flavor:** *"Everything you've ever played, indexed."*
- **Mechanic:** Grows **+10 Chips per distinct poker hand type played this run** (each new type = a **node**). **Recall:** when the played hand's type is already a node, Atlas gives **double** its Chips this hand.
- **Balance / vanilla comp:** run-scaling chips in the **Castle**/**Blue Joker** band — 6 nodes = +120 on recalls, ceiling 12 nodes = +240, approaching **Stuntman** without the hand-size penalty. Rewards early diversity, then settling into what you've indexed — build the graph, then query it.
- **Hooks:** `context.before` (insert node / flag recall), `context.joker_main`. `loc_vars` shows node count.
- **blueprint_compat:** `true`
- **Sprite:** amber constellation of square nodes on near-black, 1px dark-gray edges; one node lit bright with a ring (the recall); index ticks along the bottom.

### 8. Murmur — voice-to-text, quiet

- **Rarity:** 1 (Common) · **Cost:** $4
- **Flavor:** *"One take. No edits."*
- **Mechanic:** **+5 Mult.** While **no discards have been used this round**: **+10 Mult instead** — said in one take. First discard drops it to +5 until next round.
- **Balance / vanilla comp:** baseline **Joker** (+4, $2); ceiling under **Mystic Summit** and far under **Half Joker**. Fair always-on floor with a discipline bonus — the pack's quiet workhorse; natural partner to Banner/Delayed Gratification no-discard play.
- **Hooks:** `context.joker_main`, `context.pre_discard` (round `edited` flag), `context.setting_blind` (clear). `loc_vars`: ONE TAKE / EDITED.
- **blueprint_compat:** `true`
- **Sprite:** single clean amber waveform across a dark field, one gentle peak, flatlining into a full stop; tiny mic glyph; more empty black than any other card.

---

## Pack-Level Design

### Synergy web (deliberate)

- **Orbit → Atlas** (the spine): Orbit pays retriggers only on *fresh* hand types; Atlas pays double only on *recalled* ones. Every hand pays exactly one; alternating hand types feeds both. Fresh work gets verified; known work gets recalled.
- **Hermes between Orbit and Atlas**: the flagship sandwich — alternating sides while you alternate hand types. The mod's poster board.
- **Greenlight + one premium joker**: park your best joker (Orbit is the dream) alone in the queue for a reliable double. **Warning pairing:** never park Emissary right of Greenlight — approving a deployed Emissary wastes the approval (rotation does NOT skip debuffed jokers; that's the punishment for sloppy org charts).
- **REM + shop junk**: $1-2 commons as prune-food; the +$2 rider keeps the fleet off the kill list and feeds Swashbuckler/Gift Card economies.
- **Stoker + Murmur** (+ Half Joker): the small-hands no-discard lifestyle.
- **Emissary + Stoker drip**: quietly out-earns Golden Joker into interest breakpoints.

### The "fleet on the table" fantasy

Five slots is the vanilla board, and five fleet jokers is the real system running: **Greenlight** gates the queue on blind select, **Hermes** relays between lanes every hand, **Orbit** verifies everything fresh, **Atlas** indexes every hand type the run has played, and when the Boss falls — end of the week — **REM** dreams: prunes the weakest thing on the board and wakes up stronger. Blind = work window, ante = week, boss = deadline. Emissary out on mission during the small stuff and always home for the deadline, Stoker burning leftover budget at reset, Murmur transcribing in one take underneath it all. Running all 8 requires Negative editions or slot vouchers — correct: it should feel like scaling the fleet past default capacity. When it happens, the board *is* the ops room.

### Anti-frustration notes

- **Telegraphing over surprise:** REM shows its marked victim during the Boss Blind, never eats Eternals, prefers non-Fleet food. Greenlight's approved joker gets a visible badge; deterministic rotation. Emissary visibly dims with a DEPLOYED tag. Orbit/Murmur expose FRESH/STALE and ONE TAKE/EDITED — no hidden flags anywhere.
- **Guaranteed floors:** Orbit's first hand always fresh; Emissary never absent for a Boss; Stoker's 5-card hands add zero rather than punish; Murmur degrades to +5, never nothing.
- **No feel-bad locks:** Greenlight debuffs clear instantly on sell; REM does nothing rather than eat your only joker; Hermes shows "Incompatible!" instead of silently failing.
- **Scaling permanent where the metaphor says so** (Emissary, REM, Atlas persist; Stoker/Murmur reset at round boundaries, never mid-blind).

### Build order — S-tier weekend (ship these 5 first)

Risk-ordered; each joker teaches exactly one new SMODS concept:

1. **Murmur** — conditional mult + round flag. Proves the loc_txt/config/atlas pipeline.
2. **Atlas** — persistent per-run state + `loc_vars` + `context.before` detection.
3. **Stoker** — per-hand math on `context.full_hand`, blind-end reset, the pack's only `calc_dollar_bonus`.
4. **Emissary** — blind-select state machine + permanent xMult + skip handling.
5. **REM** — joker destruction (`start_dissolve`), boss detection, sell-value mutation. The showpiece.

3 Common / 1 Uncommon / 1 Rare — a coherent playable mini-fleet. **Week two:** Orbit (`context.repetition` edge cases), Hermes (copy tech, the hardest pattern), Greenlight (the most custom machinery). Stretch if Saturday goes clean: Orbit.

---

## Hello World First

Before any of the above: ship the **hello-world joker** — a +4 Mult vanilla clone with a placeholder sprite — and see it in a shop in a real run. Mod loads, atlas resolves, localization renders, `calculate` fires. Nothing else starts until that screenshot exists. Every joker after it is the hello-world plus exactly one idea.

## Balatrobot Headless Test Plan

Harness: Balatro + SMODS + balatrobot API, fixed seed, Red Deck, White Stake; inject jokers/hands via the bot/debug interface; assert on score/state deltas. One assertion per mechanic:

```python
def test_murmur_one_take():      # 0 discards used, then after a discard
    assert score_mult_delta(no_discards=True) == 10 and score_mult_delta(no_discards=False) == 5

def test_atlas_recall_doubles():  # Pair, High Card (2 nodes), then Pair again
    assert chips_from(atlas, hand="pair", replay=True) == 2 * 10 * 2

def test_stoker_stokes_and_burns():  # 3-card hand, then round end with 60 charge
    assert chips_from(stoker, hand_index=2) == 20 and dollars_at_round_end(stoker) == 1

def test_emissary_mission_cycle():   # Small Blind out, Big Blind home
    assert not scoring_during(emissary, blind="small") and xmult_during(emissary, blind="big") == 1.25

def test_rem_consolidates():      # beat boss with 2 other jokers, sell values $2 and $5
    assert destroyed_joker.sell_value == 2 and rem.xmult == 1.5 and neighbor.sell_value_delta == 2

def test_greenlight_approves_one():  # 2 jokers right of Greenlight
    assert debuffed_count_right(greenlight) == 1 and trigger_count(approved_joker) == 2

def test_orbit_fresh_only():      # Pair then Flush then Flush
    assert retriggers_on(orbit, hand="flush", prev="pair") and not retriggers_on(orbit, hand="flush", prev="flush")

def test_hermes_alternates():     # Joker (+4 Mult) left, Greedy Joker right
    assert copied_side(hermes, hand_no=1) == "left" and copied_side(hermes, hand_no=2) == "right"
```

Green across all eight = the fleet is real.
