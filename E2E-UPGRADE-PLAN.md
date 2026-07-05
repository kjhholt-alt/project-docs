# E2E Upgrade Plan — making "verified" mean more

*Synthesis 2026-07-05, from two converging lanes: [E2E-RESEARCH-2026.md](E2E-RESEARCH-2026.md) (external state of the art, top-5 adopt list) + the local capability inventory (ground-truth audit). Both independently reached the same diagnosis.*

## The diagnosis, in one line

**Every verification loop the fleet owns tests a FROZEN STATE (does it render / does it build); none test a SEQUENCE OF INTERACTIONS (does it survive a resize, a drag, a flow) — and that's exactly the bug class that ships.** Proof: the fit()-on-resize bug lived in a documented 16-hour blind window (static shots fixed 2 render bugs at 06:08; the interactive-resize regression wasn't even *representable* by the harness until 22:13).

## What we have (inventory verdict)

| Loop | State |
|---|---|
| Orbit offscreen screenshots (.recon + env seams) | LIVE but static-only — every seam is a launch flag |
| Web E2E | Zero in-repo tests in ALL 5 SaaS apps; webexplore/webbench skills real but stale ~2 months; webbug never filed a bug |
| Game bridges (GameForge Godot MCP, factorio mock-API, war-table reducer) | LIVE, deliberately bounded; the fleet independently invented the industry-standard RPC-bridge pattern |
| Windows-MCP / computer-use tools | Installed, used interactively, wired into ZERO automated loops (and structurally can't test Orbit — GPUI has no accessibility tree) |
| Bridge self-test ([assistant-v1 self-test] round-trip) | Real but a one-off act, not a reusable module |
| Greenlight verify bars (26 sampled) | ~half cite E2E-flavored evidence; 10/26 are doc-integrity only |
| Telemetry-as-verification (headroom flight) | LIVE, mature, domain-narrow |

## The build order (synthesized from both lanes)

**B1 — The Sweep+Diff primitive** *(research adopts #1+#2 fused — they're one build; ~1.5 days; the single biggest lever per BOTH lanes)*
One reusable tool, three backends:
- Orbit/native: launch offscreen → SetWindowPos ladder (6-8 sizes incl. <900px compact) → screenshot each step → odiff vs per-project baselines → agent triages ONLY failing diffs.
- Web: Playwright viewport sweep (375/768/1280/1920 + mid-flow resize) → toHaveScreenshot/odiff.
- Game: bridge-triggered state dumps + screenshots at known states → odiff.
**First verify (the satisfying one): revert the fit-fix in a worktree — the sweep MUST go red.** Then re-run the two render-bug commits vs pre-bug baselines — the diff gate must flag both with zero LLM involvement.

**B2 — Web lane revival** *(half-day pilot + ~1h/project)*
Playwright Test Agents (`npx playwright init-agents --loop=claude`) on wishlist-tracker first: planner explores, generator commits a real TypeScript suite, CI runs it free forever. Then roll to the SaaS four. Separately: re-schedule webexplore audit-all (infra exists, just stale).

**B3 — Orbit `--test-bridge` RPC seam** *(2-4 days; highest ceiling — the anti-stale-trust primitive)*
JSON-RPC on a debug flag: dump view/layout/state tree, inject events (click-by-id, key, resize), screenshot-on-demand. The ONLY route to real Orbit E2E (no UIA, no AccessKit, no headless GPUI — even Zed calls their UI tests "eye tests"). Feeds Orbit 0.5 and every future GPUI verify bar. First verify: bridge script reproduces the resize bug pre-fix, passes post-fix.

**B4 — Game harness standardization** *(~1 day/project on existing bridges)*
GdUnit4/GUT headless in Godot verify bars; seeded random-action campaigns on war-table's reducer (N seeds × invariant checks; failing seed = exact replay). First verify: 1,000-seed overnight campaign — violations found or robustness proven, both wins.

**B5 — Codify the harness template + self-test module** *(~1 day glue)*
The per-lane-type table from the research doc becomes a skill/template lanes grab by project type; the bridge round-trip self-test becomes a named reusable module.

## Standing doctrine changes (effective immediately, no build needed)

1. **Verify bars for anything that renders must include a sweep** once B1 exists; until then, lanes must note "static-only visual verify" honestly in reports.
2. **LLMs are never the comparator** — pixel diff decides; agent triages the diff. (Token math: a screenshot read ≈ 1.6-2.4k tokens vs a near-free odiff exit code.)
3. **Computer-use/vision driving = weekly exploratory probe only**, never a merge gate (52% success on Windows benchmarks).
4. **New GUI features ship with a seam** (env flag, RPC method, or a11y node) — tonight proved seams are what make apps verifiable.

## Don't-bother (both lanes agree)

Vision computer-use as a gate · commercial QA SaaS + paid visual services · WinAppDriver (dead) · driver-level input · vision-first web frameworks · waiting for GPUI accessibility · "agent harness" frameworks (name collision — they run agents, not tests).
