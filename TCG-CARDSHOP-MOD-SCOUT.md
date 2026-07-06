# TCG Card Shop Simulator — Modding Scout Report (gl-0096)

*Scout only, no build. Consolidates gl-0094/gl-0095. Origin: Kruz's mid-game
note 2026-07-05 ("mod the trading card sim... played a Pokemon card mod and
loved it"). The original scout guessed Balatro (`BALATRO-MOD-SCOUT.md`,
superseded 2026-07-05) — ground truth confirms the actual game is
**TCG Card Shop Simulator**, 145.5h in Kruz's library, per `user_steam_library`
memory. All claims below are web-verified against live Steam/Nexus/Thunderstore/
GitHub pages, not training data.*

---

## TL;DR

**Verdict: GO.** This is an actively modded, offline single-player Unity game
with a mature BepInEx/Harmony scene, JSON-driven content pipeline, and a
dedicated modding API team. It cleanly resolves to **MOD** under the
overlay-vs-mod doctrine — no anti-cheat, no competitive online concerns, and
the developer-adjacent tooling (CORE_API_TEAM) implies tacit mod support. The
specific "Pokemon card mod" Kruz loved is almost certainly **Real TCG
Overhaul** (38K downloads) — a real-card reskin. That lane is already crowded;
the higher-value original angle is a fully homebrew card set using the same
pipeline, or a light Discord-bridge mod that nothing currently does.

---

## 0. Overlay-vs-mod doctrine — applied first

Per `feedback_overlay_vs_mod_route` / `GAME_TOOLS_ROUTE_MAP.md`:

1. Online + anti-cheat/ToS risk? **No.** Steam tags: Singleplayer, Simulation,
   Management. The only social feature is an opt-in "host game" event (invite
   players to play card games in your shop) — not competitive ranked play, no
   anti-cheat.
2. Offline single-player + moddable (engine + mod loader)? **Yes.** Unity +
   BepInEx, huge Nexus/Thunderstore presence (see §2).
3. → **Rule 2 applies: MOD.** True in-game state, one-click distribution via
   Nexus/Thunderstore, no ban risk. Added to `GAME_TOOLS_ROUTE_MAP.md`.

---

## 1. Ground truth (web-verified)

| Fact | Value | Source |
|---|---|---|
| Game | TCG Card Shop Simulator, Steam appid **3070070** | [Steam](https://store.steampowered.com/app/3070070/TCG_Card_Shop_Simulator/) |
| Developer/Publisher | OPNeon Games | Steam store page |
| Release | Early Access, Sept 2024 | Steam store page |
| Reviews | "Overwhelmingly Positive," 97% of 32,861 | Steam store page |
| Tags | Simulation, Management, Economy, **Singleplayer**, Trading Card Game | Steam store page |
| Engine | **Unity 2021.3.38f1** | [FyreDay/TCG-CardShop-Sim-APClient README](https://github.com/FyreDay/TCG-CardShop-Sim-APClient) |
| Runtime | **Mono** (not IL2CPP) — confirmed by BepInEx pack targeting "Mono Unity games" | [Nexus: BepInEx Pack](https://www.nexusmods.com/tcgcardshopsimulator/mods/8) |
| Mod loader | **BepInEx** (+ Harmony patching), not MelonLoader | [Thunderstore BepInExPack](https://thunderstore.io/c/tcg-card-shop-simulator/p/BepInEx/BepInExPack/) — 32M downloads |
| Mod manager | r2modman (10M dl), GaleModManager (478K dl) | Thunderstore |
| In-game mod UI | F1 opens a mod settings window | [dotesports how-to-mod guide](https://dotesports.com/indies/news/how-to-mod-tcg-card-shop-simulator) |
| Modding API team | **CORE_API_TEAM** ships an Example Mod + Configuration Manager as scaffolding for other modders | [Thunderstore CORE_API_TEAM](https://thunderstore.io/c/tcg-card-shop-simulator/p/CORE_API_TEAM/) |
| Content pipeline | New cards/expansions are **JSON manifests** (CardExpansion + `.catalog.json`) + AssetBundle art, built via community tools (TCGCardImporter, EnhancedPrefabLoader) — no C# required for pure content mods | [Nexus: TCGCardImporter](https://www.nexusmods.com/tcgcardshopsimulator/mods/1120) |
| Community support | Dedicated Thunderstore modding Discord | search results |
| Multiplayer mod | **CardShopCoop** — true co-op over Steam/LAN, actively updated (117 dl, "updated 4 hours ago" at scout time) | Thunderstore |

**No headless/API-bot precedent found** (unlike Balatro/balatrobot or KSP/kRPC)
— this is a content + light-behavior modding target, not an automation/agent
target. That's fine; it matches the ask (mod the game he plays, not build an
agent to play it).

---

## 2. Scene health — 24+ Thunderstore categories in active use

Tools, Mods, Misc, Audio, Sets, Expansions, Cards, Items, Phone Related,
Furniture, Decoration, Shop, Apps, Sellables, QOL, Themes, NPCs, Cheats, Asset
Replacement, AI Generated.

**Already crowded (don't rebuild these):**
- **Card/set reskins**: Real TCG Overhaul (Pokemon+Yu-Gi-Oh, 38K dl),
  PTCGO Economics, More Card Expansions, Add Entirely New Cards Mod
- **Economy/analytics**: TCG Shop Economy (dynamic markets, pity system,
  stat-tracker phone app), Detailed Game Stats, Auto Set Prices,
  Collection Tracker
- **Inventory automation**: Auto Stocker, One Click Restock,
  Better Worker Restock — all mature, hotkey- or worker-AI-driven
- **Art/texture**: ArtExpander, CustomTextureReplacer, Binder Overhaul
- **Debug tooling**: UnityExplorer (570K dl)

This is a genuinely healthy, still-active scene (co-op mod updated hours
before this scout ran) — not a dying or thin one.

---

## 3. Candidate mod ideas (2–3, effort-estimated)

### A. Original homebrew card expansion — **S, one weekend**
Not another real-TCG reskin (saturated lane, §2) — a fully original card set
using the proven CORE_API_TEAM pipeline: CardExpansion JSON + `.catalog.json`,
card art (AI-generated or drawn), packaged with TCGCardImporter +
EnhancedPrefabLoader. Zero C#/Harmony required — pure data + assets, the same
pattern that let `Real TCG Overhaul` reach 38K downloads.
- **Effort driver**: art volume (one image per card × rarity tiers), not code.
- **Kruz-flavor option**: reuse card frames/IP from his own homebrew universes
  (Command Zone Marvel decks, Iron Gauntlet, CK3 Second Rome) instead of a
  real-world TCG — turns the shop sim into a display case for his own game
  worlds rather than a copy of an existing one.
- **Unknown to verify before building**: exact CardExpansion JSON schema and
  whether EnhancedPrefabLoader is a hard dependency — read its Nexus page
  and the CORE_API_TEAM Example Mod wiki page directly.

### B. Discord shift-report bridge — **S, one weekend**
A light BepInEx/Harmony mod hooking the game's end-of-day/shift-close event,
POSTing a summary (revenue, top sellers, satisfaction) to a Discord webhook —
piggybacking on Kruz's existing Discord-hub + webhook infra
(`project_session_metrics`, `discord_hermes_pipeline` pattern) instead of
another in-game phone-app UI, which is what every existing stats mod
(Detailed Game Stats, TCG Shop Economy) already does. This is the one gap the
scene hasn't filled: **no existing mod exports shop stats externally.**
- **Effort driver**: finding the right Harmony patch target (one postfix on
  the day-end/checkout method) via dnSpy/ILSpy decompilation of the Mono
  assemblies — well-trodden since the game is Mono, not IL2CPP.
- **Unknown to verify before building**: the exact method to patch (needs a
  decompile pass — no public docs list game-internal method names).

### C. Zero-build option — play `CardShopCoop` with friends
Not a build item: **CardShopCoop** (true co-op, Steam/LAN, actively
maintained) already exists and pairs naturally with Kruz's current
MTG-with-friends Discord pattern (`project_command_zone`,
`project_readycheck_bot`). Cheapest way to get more value from the 145.5h
game immediately — install, invite, play. Worth a mention alongside B/C
since it costs zero dev time.

**Recommendation**: If Kruz wants to build something, **A** is the closest
match to "the Pokemon mod he loved" but original instead of derivative, and
the lowest-risk entry point (no C#). **B** is the more interesting infra fit
(reuses his existing Discord plumbing) but needs one decompile session to
de-risk. Neither is scheduled — this is scout-only per the item.

---

## 4. Verification performed (the "prove it" for a scout-only item)

No code to build/test. Proof = every load-bearing claim above was checked
against a live, independently-fetched source at scout time, not recalled from
training data:
- Steam store page fetched directly (appid 3070070, dev, tags, review score).
- Unity version + Mono runtime cross-confirmed from two independent sources
  (GitHub APClient README + Nexus BepInEx pack description).
- Mod counts/download figures pulled from live Thunderstore category page.
- Modding pipeline (JSON/CardExpansion, CORE_API_TEAM, TCGCardImporter) pulled
  from live Nexus mod pages, not assumed.
- Checked for redundancy before proposing ideas — searched economy/stats,
  restock/automation, and card-import categories specifically to confirm what
  already exists, so candidates A/B don't duplicate shipped mods.

---

## Sources

- [Steam: TCG Card Shop Simulator](https://store.steampowered.com/app/3070070/TCG_Card_Shop_Simulator/)
- [GitHub: FyreDay/TCG-CardShop-Sim-APClient](https://github.com/FyreDay/TCG-CardShop-Sim-APClient)
- [Nexus: BepInEx Pack](https://www.nexusmods.com/tcgcardshopsimulator/mods/8)
- [Nexus: Pre-configured BepInEx + Configuration Manager](https://www.nexusmods.com/tcgcardshopsimulator/mods/2)
- [Nexus: TCGCardImporter](https://www.nexusmods.com/tcgcardshopsimulator/mods/1120)
- [Nexus: TCG Shop Economy](https://www.nexusmods.com/tcgcardshopsimulator/mods/1088)
- [Nexus: Detailed Game Stats](https://www.nexusmods.com/tcgcardshopsimulator/mods/903)
- [Nexus: Auto Set Prices](https://www.nexusmods.com/tcgcardshopsimulator/mods/9)
- [Nexus: Auto Stocker](https://www.nexusmods.com/tcgcardshopsimulator/mods/43)
- [Nexus: Collection Tracker](https://www.nexusmods.com/tcgcardshopsimulator/mods/867)
- [Thunderstore: TCG Card Shop Simulator community](https://thunderstore.io/c/tcg-card-shop-simulator/)
- [Thunderstore: BepInExPack](https://thunderstore.io/c/tcg-card-shop-simulator/p/BepInEx/BepInExPack/)
- [Thunderstore: CORE_API_TEAM mods](https://thunderstore.io/c/tcg-card-shop-simulator/p/CORE_API_TEAM/)
- [dotesports: How to mod TCG Card Shop Simulator](https://dotesports.com/indies/news/how-to-mod-tcg-card-shop-simulator)
