# E2E Testing for an Agent Fleet — 2026 State of the Art & Adoption Ranking

*Researched 2026-07-05 (cloud lane) on Kruz's ask: "best possible ways to improve our agents' computer use / our own loops to E2E test better." Companion: the local capability inventory (separate lane, E2E-INVENTORY doc when it lands) and the synthesis plan to follow.*

**One-line thesis: the fleet's instinct (screenshots catch what unit tests miss) is right — the 2026 upgrade is to make that signal DETERMINISTIC (pixel-diff gates, interaction sweeps, RPC seams) and spend LLM tokens only on authoring tests and triaging failures, never on being the comparator.**

---

## 1. Computer-use agents for testing

- **Anthropic computer-use tool**: still beta; current version `computer_20251124` (adds `zoom` action). Recommended 1024×768–1280×720. Sonnet-class ~72.5% on OSWorld (human 72-87%); **Windows-specific benchmarks much worse** — WindowsAgentArena best published 52.5% (CoAct-1) vs 74.5% human. Cost ~$0.006-0.018/action, 2-5s/action, 50-step flow ≈ $0.30-1.20. **Honest read: a smoke-tester, not a regression gate.** ([docs](https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool), [OSWorld](https://os-world.github.io/), [WindowsAgentArena](https://microsoft.github.io/WindowsAgentArena/))
- **The pattern that works on Windows: UIA accessibility tree, not pixels** — FlaUI (maintained .NET UIA3), FlaUI-MCP (snapshot→refs→act, "no coordinate guessing"), Windows-Use; our Windows-MCP server is the same family. **WinAppDriver is dead** (no release since 2020).
- **Critical caveat for Orbit:** GPUI apps expose essentially NO accessibility — Zed itself has zero practical Windows screen-reader support; AccessKit-in-GPUI is acknowledged long-horizon ([Zed #6576](https://github.com/zed-industries/zed/discussions/6576)). UIA-tree driving cannot test Orbit today.

## 2. Browser E2E in the agent era

- **Playwright MCP / a11y-tree-first is settled**: snapshots ~200-400 tokens vs thousands for DOM or ~1.6-2.4k image tokens per screenshot — 5-10x cheaper than screenshot-read loops for interaction verification. Screenshots stay only for visual assertions.
- **Playwright Test Agents (planner/generator/healer) are official and Claude Code-native**: `npx playwright init-agents --loop=claude` (Playwright ≥1.56). Planner explores → Markdown plan; generator emits real TypeScript tests with live-verified selectors; healer repairs failing locators. **Agent pays exploration once; CI runs deterministic tests free forever.** ([docs](https://playwright.dev/docs/test-agents))
- **Self-healing: proven for maintenance (75%+ of selector failures), oversold for authoring.** Generation is the win for small suites.
- **Visual regression**: Playwright `toHaveScreenshot()` (pixelmatch); **odiff** is the 8x-faster drop-in ([odiff](https://github.com/dmtrKovalenko/odiff)). Skip paid services (Percy/Chromatic/Argos) — solo operator needs local baselines + diff-on-fail, not review dashboards. **Consensus: VLMs are NOT the comparator — pixel diff decides, vision only triages.**
- **Exploratory testing agents**: the SaaS products are just agent + browser MCP + "try to break it" + error listeners — our /webexplore + /webbug skills ARE this category, self-hosted.

## 3. Testing GUI apps without a display (the Orbit problem)

- **GPUI's own answer**: `#[gpui::test]` + TestAppContext (headless, no GPU) + VisualTestContext — but a Zed maintainer says UI tests are "pretty much eye tests right now"; no headless full-instance story, no timeframe ([Zed #33322](https://github.com/zed-industries/zed/discussions/33322)). **Even GPUI's creators don't black-box-test GPUI apps** — they compensate with massive deterministic in-process testing (TestDispatcher controls scheduling/randomness).
- **Gold-standard Rust reference: egui_kittest** (Rerun) — in-process harness + AccessKit semantic tree + wgpu offscreen snapshots. Can't drive Orbit (no AccessKit in GPUI) but validates the architecture to copy.
- **Real-window on headless Windows**: HDMI dummy plug or [Virtual Display Driver](https://github.com/VirtualDrivers/Virtual-Display-Driver) (relevant for the MS-A2's screenshot loops — the fleet-node plan's dummy plug is confirmed correct).
- **Input simulation tiers**: UIA patterns (needs a11y — not GPUI) → **SendInput** (real OS-level input, works on any focused window — sufficient for Orbit smokes) → driver-level (only raw-input games, skip). **Resize is scriptable with none of this**: SetWindowPos sweeps exercise the exact WM_SIZE→layout→refit path that shipped this week's bug.

## 4. Game testing automation

- **RPC-bridge + headless is validated**: balatrobot (JSON-RPC into the game + pytest + headless/fast flags) is the canonical shape; jackdaw adds deterministic-sim + live-game conformance. **Our fleet independently converged on this** (war-table reducer, GameForge 20-tool Godot MCP bridge) — confirmed right bet.
- **Engine headless modes solved**: Godot `--headless` + GdUnit4/GUT (JUnit XML, GH Action); Unity `-batchmode -runTests` (never `-nographics` for rendering — matches our memory).
- **Determinism seams / DST**: seeded fuzz campaigns over deterministic reducers (control time/randomness/concurrency; replay any failure by seed) — war-table's reducer is already DST-shaped; the upgrade is seeded campaigns × invariant checks.
- **Agent playtesting**: LLM agents hit 79% code coverage on a match-3 game in the literature; useful as balance probes; everything routes through a bridge. Vision-only game agents = research toys.

## 5. Harness-as-product

No off-the-shelf harness covers web+native+game+CLI; the glue is thin (~1-2 days to codify a fleet template):

| Lane type | Explore/author | Interaction gate (deterministic) | Visual gate | State seam |
|---|---|---|---|---|
| Web | Playwright planner→generator (--loop=claude) | Playwright tests + viewport sweep (375/768/1280/1920 + mid-drag resize) | toHaveScreenshot/odiff vs baselines; agent reads diff only on fail | console+network listeners |
| Native (Orbit) | agent + debug bridge | #[gpui::test] for logic; real-window smoke: SetWindowPos sweep + SendInput | screenshot per step → odiff | **debug JSON-RPC seam: dump layout/state, inject events** |
| Game | agent + RPC bridge | seeded action replays over deterministic reducer; engine headless runner | bridge-triggered screenshots at known states → odiff | war-table reducer, GameForge MCP (have) |
| CLI/Python | agent-written pytest | golden-file asserts | n/a | structured JSON output flag |

Unifying primitives: (a) a **state seam** (a11y tree or RPC dump — never pixels for state), (b) a **seeded interaction sweep** (the shipped bug class), (c) a **pixel-diff gate with agent triage only on fail**.

---

## TOP 5 ADOPT (ranked for this fleet)

1. **Pixel-diff gate on existing screenshot loops (odiff)** — baselines per project; agent sees only the diff on fail. Deterministic, near-free, turns the render-bug class into a hard CI signal. *Effort: ~half a day.* First verify: re-run the two render-bug commits against pre-bug baselines — gate must flag both, zero agent involvement.
2. **Interaction-sweep primitive ("resize storm") in every verify bar** — web: viewport sweep; Orbit: SetWindowPos loop + screenshot each step; games: window-mode toggles via bridge. *Effort: 1 day, reusable fleet-wide.* First verify: revert the resize-refit fix in a worktree; sweep must go red.
3. **Playwright Test Agents for web lanes** — `init-agents --loop=claude` per project; exploration becomes a committed deterministic suite. *Effort: half-day pilot + ~1h/project.* First verify: wishlist-tracker suite covering add-item/price-refresh; broken route must fail; healer must NOT mask a real functional break.
4. **Debug RPC seam in Orbit (`--test-bridge`)** — JSON-RPC: dump view/layout/state, inject events (click ref, key, resize), screenshot-on-demand. The ONLY route to real Orbit E2E in 2026 (no UIA, no AccessKit, no headless GPUI). *Effort: 2-4 days; highest ceiling — the anti-stale-trust primitive.* First verify: bridge script reproduces the resize bug pre-fix, passes post-fix.
5. **Standardize game-lane harness** — GdUnit4/GUT headless in Godot verify bars; seeded random-action campaigns on war-table-class reducers (N seeds × invariants, failing seed = exact replay). *Effort: ~1 day/project on existing bridges.* First verify: 1,000-seed overnight campaign on war-table; violations found or robustness proven — both wins.

## DON'T-BOTHER

- **Vision/pixel computer-use as a routine test driver** (72.5% OSWorld / ≤52.5% Windows; keep as a weekly exploratory probe, never a merge gate)
- **Commercial autonomous-QA SaaS + paid visual services** (they sell a coordination dashboard a solo operator doesn't need)
- **WinAppDriver/Appium-on-Windows** (dead since 2020)
- **Driver-level input injection** (kernel-invasive; SendInput + bridges cover everything we build)
- **Vision-first web frameworks (Magnitude)** (a11y-first is strictly cheaper for apps we own)
- **Waiting for GPUI accessibility** (long-horizon; the bridge makes it moot)
- **"Agent harness" frameworks (OpenHarness etc.)** (name collision — they orchestrate agents, don't test apps)

*(Primary sources inline; full citation list in the research lane transcript.)*
