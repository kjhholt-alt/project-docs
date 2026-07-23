# Switchyard — Cross-Agent Mission Router Thesis

**Date:** 2026-07-22
**Status:** Recommended next lane; shadow-mode proof first
**Decision:** Do not build or fork an editor yet. Trial Zed as an alternate shell and build the missing routing brain as an editor-independent local tool.

## The one-line product

Switchyard takes one goal and produces a transparent, evidence-backed route for the whole mission: agent harness, exact model and effort, formation, execution surface, verification bar, and fallback ladder.

Cursor's new Router chooses a model for a request. Switchyard should choose and govern the entire work package.

## Why this is the right lane

Cursor announced its Router on 2026-07-22. It classifies a request before a model runs, considers context and task complexity, and offers Intelligence, Balance, and Cost modes. Cursor reports frontier-quality results at roughly 60% lower cost in its online comparison and 30–50% lower cost for early enterprise users routed away from an all-Opus baseline. It also reduces context overhead by loading uncommon tool descriptions only when needed.

The launch is for Teams and Enterprise plans across Cursor's desktop, web, iOS, CLI, and SDK surfaces; it is not evidence that the feature is enabled on this machine's current Cursor account. Cursor also hides the selected routed model by default, although admins can expose it. Switchyard should make the route visible by default because the receipt is part of the product.

Primary sources:

- <https://cursor.com/blog/router>
- <https://cursor.com/en-US/changelog>

That is real and important, but narrower than the problem on this machine. Kruz is choosing among Codex, Claude Code, Hermes jobs, local/off-box execution, different subscription pools, different proof bars, and sometimes different user interfaces. API price is only one constraint. Quota burn, latency, machine load, rework, verification strength, and whether an artifact is actually accepted all matter.

## What already exists here

| Layer | Existing asset | Its job | Boundary |
|---|---|---|---|
| Goal compilation | Workflow Compiler | Messy ask → archetype, lanes, proof, and human gates | Compiles only; does not run |
| Mission evidence | Model Draft Room | Exact model/effort observations and formation proof | Does not mutate the live router |
| Cost evidence | Agent Economics Observatory / TPVS | Tokens and cost per verified shipped unit | Read-only; does not dispatch |
| Current model policy | Greenlight routing table | Task-class-to-model recommendations | Mostly Anthropic-family routing today |
| Execution and safety | Hermes + Greenlight | Queue, gates, audit, recovery, safe execution | Remains the only execution path |
| VS Code shell | Flight Deck | Sessions, decisions, artifacts, controls | Claude owns this lane tonight |
| Native cockpit | Orbit | Local truth and future ACP client | Not the place to duplicate policy |
| Alternate editor shell | Zed | ACP-native external-agent UI | A trial surface, not our moat |

The missing product is not another classifier or dashboard. It is the live policy layer that unifies these assets and emits one inspectable decision receipt.

## Product boundary

```text
Goal
  ↓
Switchyard: classify → gate → route → attach proof → define fallback
  ↓                         ↓
Route receipt           Shadow-learning log
  ↓                         ↑
Greenlight / Hermes → Codex or Claude → verification → accepted outcome
  ↑
Flight Deck / Zed ACP / terminal / Orbit
```

Switchyard owns route compilation and receipts. It does not own editor UI, queue state, agent runtimes, approvals, or repo writes.

## The route decision

For each mission, Switchyard should select:

1. **Mission class:** audit, build, fix, design, scout, docs, hot ship, or an honest unknown.
2. **Risk and gates:** local/read-only, repo-writing, hot/public, account, spend, or human-only.
3. **Harness:** Codex, Claude Code, Hermes workflow, or HOLD.
4. **Exact lane:** provider, model, reasoning/effort, and execution location.
5. **Formation:** solo by default; reviewer/tandem only when evidence predicts a net win.
6. **Context pack:** only the project rules, tools, sources, and files required for the lane.
7. **Verification bar:** tests, diff checks, live proof, source proof, or human gate.
8. **Fallback ladder:** retry, increase effort, switch model/provider, add reviewer, or stop.

Every decision must display why it was made, evidence depth and freshness, estimated quota/time impact, and what would cause escalation.

Workflow Compiler should remain the front end: it already converts a raw ask into Build, Review, Research, Productize, Recovery, or Demo archetypes with lanes, proof rules, and decision cards. Switchyard starts from that packet, maps each lane to the existing Greenlight kind/size taxonomy, and makes the narrower execution decision. No new natural-language classifier is needed for the shadow proof.

## Three user modes

- **Quality:** strongest qualified lane; still no unproved multi-agent fan-out.
- **Balance:** cheapest lane with credible evidence that it clears the verification bar.
- **Quota Saver:** cheapest credible first attempt with an explicit verified escalation ladder.

These are not just price settings. They should account for subscription quota, current meter pressure, latency, cache/context cost, machine headroom, and expected rework.

## Governing rules

1. **Gates before scores.** A cheap model never bypasses a safety, account, public-action, or Kruz-only gate.
2. **Single agent is the prior.** Complexity has to earn promotion.
3. **Verification drives escalation.** Start with the cheapest credible lane, run the defined proof, then escalate on failure or uncertainty.
4. **HOLD is a successful route.** Sparse or conflicting evidence must not become fake confidence.
5. **Never hide the route.** Agent, model, effort, evidence, and fallback are visible by default.
6. **Optimize accepted output.** The north-star unit is verified accepted artifact per marginal quota burn, not cost per prompt.
7. **Learn from real outcomes.** Tests, kept/reverted code, correction turns, Kruz acceptance, time, and actual meter usage feed the next decision.
8. **No direct dispatch bypass.** Live runs enter through Greenlight/Hermes and preserve their receipts and gates.
9. **No learned classifier yet.** Cursor trained on 600k+ requests; this portfolio has dozens of exact observations, not enough to justify a black box. Begin with deterministic evidence and visible uncertainty.

## Evidence check from tonight

Flight Deck advanced independently through installed P3 while this research was running: clean `master` at `2ae52a3`, 127/127 unit tests, 8/8 extension-host tests, a read-only integration board, honestly labeled meter strip, proved ship script, fixed Codex-session mtime flake, attach/resume, responsive multi-column layout, and a live `claude agents --json` feed. That means the VS Code shell and terminal-switching problem is actively being solved by Claude. Switchyard stayed out of that repo and should later offer one stable seam before Flight Deck's existing Greenlight spawn action:

```text
goal → Switchyard route receipt → Greenlight spawn → Flight Deck session/proof view
```

The existing Model Draft Room was asked to draft this exact design mission against a confirmed `design-l` contract. Its persisted receipt selects Claude Sonnet 4.6/high and solo execution, but every exact lane has only one observation on that fixture and the receipt explicitly holds any public efficacy claim as unreplicated.

The frozen prior serial formation also remains a measured hold: the Sonnet scout → Opus lead → GPT reviewer used **6.43× tokens** and **4.69× sequential time** while scoring below the solo baseline. Therefore the first Switchyard policy must not assume that more agents are better.

The current Greenlight table is useful but uneven: it contains live recommendations across 18 task classes, while several classes still have only one or a few observations. Switchyard must carry sample depth and confidence into the receipt instead of flattening the table into certainty.

Tonight's `design-L` conflict is the exact product proof: Greenlight currently recommends Haiku from one observation per listed tier, while Model Draft Room's exact-lane receipt selects Sonnet/high from one observation per lane and holds the public efficacy claim as unreplicated. Neither source should silently overwrite the other. Switchyard should expose the disagreement, apply the mission's proof bar, and keep the route in shadow until replicated evidence improves.

Freshness also differs inside one source family right now: `headroom/state.json` is four days stale while `headroom/flight/live.json` is current. A route must timestamp and reconcile each input independently; “headroom available” is not enough. Stale posture can inform history but cannot authorize a live heavy lane.

## Example receipt shape

```json
{
  "decision": "shadow-hold",
  "mode": "balance",
  "mission_class": "design-L",
  "risk": "local-repo-writing",
  "route": {
    "harness": null,
    "model": null,
    "effort": null,
    "formation": "solo"
  },
  "evidence": {
    "confidence": "directional",
    "reason": "Sources disagree and exact evidence is unreplicated; one observation per lane."
  },
  "verification": ["schema", "unit tests", "diff check", "human product verdict"],
  "fallback": ["collect shadow outcome", "re-evaluate exact lanes", "remain held"],
  "dispatch_allowed": false
}
```

The first receipt should be boring, complete, and auditable. A beautiful UI can follow a trustworthy policy.

## Editor and terminal decision

| Surface | What it proves | Main limitation | Call |
|---|---|---|---|
| VS Code + Flight Deck | The daily-driver fleet cockpit | VS Code extension APIs are not a universal agent protocol | Keep primary |
| Zed | Lightweight Windows editor with native ACP agents and terminal threads | It will not carry Flight Deck's custom VS Code panel | First trial |
| JetBrains 2026.1+ | A second mature ACP client with registry and custom-agent support | Heavy new install and workflow change | Backup trial |
| Cursor | Best immediate look at model routing; already installed | Router policy is Cursor-owned and its terminal agent is not separately exposed on this PATH | Observe and learn |
| Custom editor fork | Total control | Huge editor/platform tax before the routing thesis is proven | Reject now |

Machine check at 2026-07-22 20:36 CDT: VS Code, Cursor 3.7.36, Codex CLI 0.145.0, and Claude Code 2.1.214 are installed. Zed, JetBrains, OpenCode, and a standalone Cursor `agent` command are not on PATH. Codex already offers JSONL events and final-output schemas; Claude offers stream JSON, structured schemas, explicit model/effort, and background sessions. The runner seam exists today.

### Buy the runtime plumbing

The late market scan changes the implementation boundary. Do not write another generic multi-CLI runner.

| Candidate | What it already owns | Safety/fit result | Call |
|---|---|---|---|
| Cursor Router | Learned model selection inside Cursor | Real results, but Teams/Enterprise and model-only | Learn from it |
| `acpx` | ACP sessions, events, permissions, cwd, cancel, export/import | Alpha; strong explicit controls | First pinned trial |
| `harness-cli` | One-shot multi-agent NDJSON normalization | Read-only exists, but default is full access | Fallback trial |
| `harness-router` | Quota fallback, health, circuit breakers, MCP/HTTP | Built-in agents bypass approvals/trust | Reject live; study |
| `context-mode` | Runtime tool-output sandbox/index and compaction support | Separate security/dependency audit required | Evaluate add-on |

`harness-router` already demonstrates that local multi-CLI execution is commodity plumbing. It is an MIT-licensed MCP/HTTP layer for Claude Code, Codex, Cursor Agent, Gemini CLI, OpenCode, Copilot CLI, generic commands, and metered fallbacks. It has route health, quota-aware ordering, circuit breakers, hot configuration, an inspectable routing decision, and cross-process SQLite state. Its source deliberately implements no quality scoring and no task-capability matrix; it only walks configured model preference, subscription tier, availability, and quota. That confirms the substrate/policy seam Switchyard needs.

- Runtime source and contract: <https://github.com/fstubner/harness-router>
- Router algorithm: <https://github.com/fstubner/harness-router/blob/main/src/router.ts>
- Built-in Claude dispatcher: <https://github.com/fstubner/harness-router/blob/main/src/dispatchers/claude-code.ts>
- Built-in Codex dispatcher: <https://github.com/fstubner/harness-router/blob/main/src/dispatchers/codex.ts>
- ACP terminal/session client: <https://github.com/openclaw/acpx>
- ACP client permission and session contract: <https://github.com/openclaw/acpx/blob/main/docs/CLI.md>
- Unified CLI/event-normalization alternative: <https://github.com/ayshptk/harness-cli>
- Unified CLI permission contract: <https://www.harness.lol/docs/agents>
- Unified CLI process lifecycle: <https://github.com/ayshptk/harness-cli/blob/main/src/process.rs>
- ACP-native terminal alternative: <https://github.com/forge-agents/forge>

The source audit rejects `harness-router` as a direct live dependency in its current form. Its built-in Claude dispatcher pre-approves Bash/Read/Edit/Write with `acceptEdits`; its Codex dispatcher uses `--full-auto --skip-git-repo-check`; its Cursor dispatcher uses `--trust` and falls back to the entire home directory if no workspace is supplied. Those defaults are incompatible with the permission and workspace contract here even if Greenlight makes a decision before launch. Study or borrow the architecture, but do not install and wire it into live work without an upstream safe mode or a reviewed patch.

The strongest terminal trial is `acpx`, an ACP-native headless client rather than an output scraper. It provides repo-scoped persistent and named sessions, Codex and Claude adapters, typed tool/diff events, cooperative cancellation, model and reasoning controls, session export/import, explicit cwd sandboxing, and per-tool permission policy. Its CLI defaults to approving reads while prompting for other capabilities, and non-interactive permission requests deny by default. For the first probe, still pass the policy explicitly: deny everything for the handshake, then allow reads only inside the disposable repo.

`acpx` is alpha and its friendly agent names auto-download currently resolved adapters through npm on first use. The trial must pin `acpx` 0.12.0, install an exact adapter version locally, and override the agent command to that reviewed path rather than accept an unversioned `npx` resolution. It is currently not installed on this machine, while Node 22.22 satisfies its runtime requirement.

Start with Codex. Direct `acpx` has been reported working on Windows even where OpenClaw's wrapper integration failed, but that is not proof for this machine. Claude ACP adapters have also had subscription-OAuth compatibility gaps. A Claude failure is an adapter verdict—not permission to add an Anthropic API key merely for the demo. The purpose is to prove existing subscription auth, instruction fidelity, permissions, structured diffs, cancellation, and resume.

`harness-cli` is the lightweight backup if ACP adapters fail. It normalizes Claude, Codex, OpenCode, and Cursor into one NDJSON stream, supports dry-run, tracks cwd, kills the subprocess tree on cancellation (including a Windows `taskkill /T` path), and exposes an explicit read-only mode. Its own default is full access, so any adapter must always pass both `--permissions` and `--cwd`. It has less session/protocol depth than `acpx`, but may be easier to pin and audit.

The machine already has Rust/Cargo for the fallback's safe source-build path. Zed 1.11.3 is also available from the current Winget catalog. None of these should be installed until Kruz chooses this as the next execution sprint.

The resulting boundary is crisp:

- **Switchyard owns:** mission classification, proof contract, evidence depth, model/effort/formation recommendation, freshness, gates, HOLD, and the route receipt.
- **Runtime owns:** subprocess invocation, streaming event normalization, route health, quota counters, circuit breakers, and transport.
- **Greenlight/Hermes own:** permissioned dispatch, queue state, approvals, recovery, and proof promotion.
- **Flight Deck/Zed/terminal own:** presentation and interaction.

Switchyard can emit an explicit target into a reviewed runtime rather than accepting that runtime's default policy. If the dependency cannot preserve that target, working directory, instruction fidelity, permissions, structured events, and cancellation, it fails the fit trial and stays out. Today, `harness-router` fails on permissions; `acpx` advances to a pinned deny/read-only ACP fit test; `harness-cli` remains the fallback.

### Try now: Zed

Zed is the best alternate editor experiment because ACP already lets it host external agents including Claude, Codex, OpenCode, Copilot, Cursor, and Pi, while each agent retains its own runtime and authentication. Its terminal threads can also run native `claude` and `codex` commands. Zed now officially supports Windows.

Trial friction should be low: Zed's default base keymap is VS Code, it also ships a Cursor keymap, and its docs provide a dedicated VS Code migration path. This lets the experiment focus on the agent workflow instead of relearning every shortcut.

Primary sources:

- <https://zed.dev/docs/ai/external-agents>
- <https://zed.dev/docs/ai/agents>
- <https://zed.dev/blog/bring-your-own-agent-to-zed>
- <https://zed.dev/windows>
- <https://zed.dev/docs/key-bindings>
- <https://github.com/agentclientprotocol/codex-acp>

The trial question is not “can Zed replace VS Code?” It is “does one lightweight shell make native terminals and ACP agents feel coherent?” Keep VS Code and Flight Deck as the daily driver during the trial.

Use a disposable repo and score five things: existing-subscription login, instruction-file fidelity, permission prompts, diff review, and session resume/import. Start with Zed Terminal Threads running the native `codex` and `claude` CLIs; this tests the editor without introducing an adapter or changing authentication. Then test Codex as an ACP External Agent because its current local authentication is already working. Test Claude ACP only after its wrapper confirms whether it can reuse Claude Code auth; do not introduce a paid API key merely to make the demo work.

The current official Codex ACP adapter is v1.1.7, released today. It advertises ChatGPT authentication, explicit approval/sandbox configuration, `INITIAL_AGENT_MODE=read-only`, and `CODEX_PATH` for selecting the already-installed Codex binary. Pin that exact adapter for the first test instead of accepting a registry or npm `latest` resolution.

The package match is unusually clean tonight: `@agentclientprotocol/codex-acp@1.1.7` depends on `@openai/codex ^0.145.0`, and this machine has Codex 0.145.0. The captured npm git heads are `307d8101` for Codex ACP 1.1.7 and `6a24a546` for `acpx` 0.12.0; the actual trial should lock their registry integrity hashes in a disposable package lock.

JetBrains is the useful control case, not the first install. Its 2026.1+ IDEs can load Claude, Codex, Cursor, and custom agents from the ACP Registry; the ACP bridge does not require a JetBrains AI subscription. If Switchyard works in both Zed and JetBrains, that is strong evidence the product boundary is truly editor-neutral.

- <https://www.jetbrains.com/acp/>
- <https://www.jetbrains.com/help/ai-assistant/acp.html>

### Build first: terminal receipt compiler over a runtime adapter

The fastest proof is a local terminal command, not an editor extension:

```text
switchyard route "fix the flaky integration test" --repo hermes --mode balance
```

It should read existing evidence and print a route receipt without launching anything. This establishes the policy contract once for every future shell. The runtime adapter is an interface with no live implementation enabled in Phase 0; a successful pinned `acpx` fit trial can fill it later.

### Build later: ACP wrapper

After shadow accuracy is credible, wrap Switchyard as an ACP agent. Zed or Orbit can then present the same route, permission prompts, streaming work, and diffs. Flight Deck can consume the same receipt contract later without becoming a second router.

### Reject for now: editor fork

A custom editor would spend the sprint on text editing, terminals, extensions, updates, language servers, accessibility, and platform bugs. None of those are the differentiated problem. Revisit only if daily use proves that Zed, VS Code, and Orbit all block a critical workflow that ACP or an extension cannot support.

## High-leverage ideas on top

These are not five new projects. They are ordered capabilities that can compound inside Switchyard.

1. **Context Contract Compiler.** Select the smallest mission-specific repo rules, sources, files, and proof contract needed for the chosen route. Do not rebuild generic tool-output compression: `context-mode` already sandboxes and indexes large tool results across Claude, Codex, Cursor, and other shells. Evaluate that separately; Switchyard's unique job is deciding what the mission needs before it starts. <https://github.com/mksglu/context-mode>
2. **Handoff Capsule.** Extend Hermes handoff v2 and its artifact packet with the Switchyard route receipt and Greenlight proof-bundle IDs. Use `acpx` session export/import for transport if it passes; Hermes remains the provider-neutral semantic/proof handoff. Do not invent a competing transcript archive.
3. **Shadow League.** Before every real mission, record what Switchyard would choose; after completion, compare that counterfactual against actual time, quota, rework, and verification. `acpx compare` can supply occasional controlled same-prompt evidence, but never turn every job into expensive fan-out.
4. **Verification Router.** Choose the proof pipeline before choosing the model. A small fix with a deterministic test may safely start cheap; an untestable design verdict may require a stronger lane or a human gate from the outset.
5. **Quota and Headroom Market.** Treat Codex quota, Claude quota, API spend, off-box capacity, and gaming/resource posture as separate pools. Route or defer work based on marginal capacity rather than a fake universal dollar price.

The first two belong in the product contract early. The last three should remain read-only signals until shadow evidence earns live control.

## Exact read-only seams

Phase 0 can be built without a new model call, daemon, database, or scraper:

| Input | Existing interface | Switchyard use |
|---|---|---|
| Compiled goal | `studio.workflow_compiler.v1` | Archetype, ordered lanes, proof, gates |
| Model policy | `~/.operator/greenlight/routing-table.json` | Task-class recommendations, sample counts, pass rates, token medians |
| Exact lane evidence | Model Draft Room `product/lane-matrix.json` and formation CLI | Exact model/effort qualification and honest HOLD |
| Agent reliability | `hermes scorecard --json` | Adapter errors, latency, activity, cost, and sample depth |
| Autonomy gates | Hermes Director `latest.json` | Run/stage/HOLD policy and Kruz-only gates |
| Quota posture | `~/.operator/meters/meters.json` | Current Claude/Codex usage and freshness |
| Machine posture | `~/.operator/headroom/state.json` and `flight/live.json` | Game/interactive/away posture and resource pressure |
| Outcome proof | Greenlight queue plus `proofs/<gl-id>.json` | Verified outcome, commands, tests, commit/artifact evidence |
| Cross-agent state | Hermes handoff v2 + ArtifactPacket | Provider-neutral continuation without transcript replay |

The initial implementation should be an adapter over these sources. If a source is missing, stale, sparse, or contradictory, the receipt degrades confidence or returns HOLD; it never fills the gap with a guessed score.

### Implementation choice

Use Python for the core. Workflow Compiler, Greenlight, Hermes, meters, headroom, and proof bundles are already Python or plain JSON, and ACP now has an official Python SDK with Pydantic models and JSON-RPC plumbing. Call Model Draft Room's existing Node formation CLI as a bounded read-only adapter rather than porting its evidence engine.

- <https://agentclientprotocol.com/libraries/python>

Keep ACP out of Phase 0. The core route function and receipt schema should be pure and independently testable; the terminal formatter, future ACP server, and future Flight Deck adapter become thin clients over it.

## Implementation receipt — 2026-07-22

The first proof is now built rather than hypothetical:

- Winget resolved and installed Zed **1.12.0** silently. Zed was not launched, made default, or given migrated editor settings.
- The local sidecar pins `@agentclientprotocol/codex-acp` **1.1.7** and its compatible `@openai/codex` **0.145.0** under `~/.operator/switchyard/codex-acp`; no API key was added.
- A headless ACP v1 smoke initialized Codex, created a session whose reported current mode was exactly `read-only`, advertised client filesystem writes and terminal access as false, and closed the session cleanly.
- The standard-library-only `switchyard` package reuses `studio.workflow_compiler.v1`, reads Greenlight, Model Draft Room, Hermes Director, Hermes capability health, Headroom, and local meters, and writes JSON/HTML receipts without dispatching work. Agent-health and quota fields are supporting diagnostics only; Claude percentages are explicitly labeled local transcript-I/O estimates.
- The first live `design-L` route is `shadow-hold`: Greenlight is stale and has one Haiku observation; the exact Draft Room receipt has one Sonnet/high observation per lane and an unreplicated efficacy claim; the model families conflict. Switchyard selects neither.
- Verification passes twenty-one unit tests, Ruff, mypy, compileall, live receipt/history generation, cross-surface supporting-warning and history-truth coverage, Zed CLI-help smoke, launcher-argument regression, and the real ACP session handshake/close.
- Each write refreshes a linked shadow-history dashboard with route-status and hold-code frequency. It explicitly labels observations as not verified outcome evidence, so volume cannot satisfy promotion gates.
- A project-local `.zed/settings.json` exposes `switchyard-codex-readonly`, and `OPEN_SWITCHYARD_LAB.cmd` refreshes the receipt before opening the project in Zed and the rendered report in the default browser when Kruz chooses to double-click it.

The ACP process is a tested surface adapter, not a dependency of the routing core. The in-app Browser exposed no instance, and the no-popup rule kept Zed closed during the autonomous pass, so screenshot/click proof and the first native Terminal Thread remain explicit user-visible gates. Direct official ACP passed; `acpx` is no longer required for the first proof and should be added only if a real headless session-management gap appears.

## First proof sprint

### The next 60-minute proof packet

If Kruz greenlights the next sprint, spend the first hour proving the shell/runtime boundary before building Switchyard:

1. **0–10:** install the current stable Zed from Winget (resolved to 1.12.0), select a familiar keymap, and open only a disposable git fixture. Do not migrate settings or make it the default editor.
2. **10–22:** run the same read-only orientation task in Zed Terminal Threads through native Codex and Claude; prove existing subscription auth and instruction files without an adapter.
3. **22–35:** add Codex ACP 1.1.7 as a reviewed custom agent in Zed, point it at the installed Codex binary, start in read-only mode, and repeat the task; inspect permission prompts, diffs, thread history, and ACP logs.
4. **35–50:** source-audit and pin `acpx` 0.12.0 plus an exact Codex adapter, then run a deny-all handshake and a disposable approve-reads session. Check Windows cwd, cancellation, JSON, and resume. No API keys and no `@latest` execution.
5. **50–60:** write a pass/reject matrix. If ACP fails, record the exact seam; test `harness-cli` later only if a normalized one-shot runner is still needed.

Done means an evidence-backed keep/reject verdict for the terminal and editor surfaces. It does not mean live dispatch, a new daemon, an editor migration, or a custom runner.

The following build hour can then implement the pure read-only Switchyard receipt compiler against a null runtime adapter. Keep product-policy proof separate from shell novelty.

### Phase 0 — read-only shadow router

1. Create a tiny standalone `switchyard` package and versioned route-receipt schema.
2. Consume the existing Greenlight routing table, Model Draft Room evidence, Hermes agent scorecard, current meters/headroom, and repo-local rules.
3. Implement `route` with Quality, Balance, and Quota Saver modes.
4. Emit `HOLD` when required evidence or verification contracts are missing.
5. Log predictions for the next 20 real missions without changing their actual dispatch.

### Promotion bar

- 20 complete shadow receipts across at least five mission classes.
- 100% visible source/freshness/confidence fields.
- Zero direct dispatches and zero gate bypasses.
- Counterfactual comparison against the route actually used and the verified outcome.
- At least one demonstrated cheaper route with equal proof, without claiming Cursor's 60% figure as ours.
- One Kruz-approved live canary only after the shadow review.

### Phase 1 — one guarded live lane

Promote one low-risk class through Greenlight/Hermes. Use verified escalation, record the full outcome, and compare its accepted-output economics against the prior route.

### Phase 2 — surfaces

1. ACP adapter for Zed/Orbit.
2. Route-receipt panel or deep link in Flight Deck after its current lane settles.
3. Optional compact terminal TUI only if the plain command proves too opaque.

## Kill criteria

This is a real-user tool for Kruz, not another abstract agent dashboard. Stop or fold it back into existing systems if any of these are true:

- Twenty shadow receipts do not change a decision or reveal a meaningful conflict.
- It cannot beat the current Greenlight heuristic on at least one verified route without weakening proof.
- Keeping adapters current costs more effort than the quota/rework they save.
- Kruz does not voluntarily use the receipt before real missions during a seven-day trial.
- The only remaining value is a prettier UI, ACP demo, or public-product story.

If the policy proves useful but the standalone command does not, merge the pure route compiler into Greenlight and keep the receipt contract. The project name is disposable; the verified policy is the asset.

## What not to build

- No new fleet dashboard.
- No replacement for Flight Deck, Orbit, Hermes, Greenlight, Model Draft Room, or Observatory.
- No always-on daemon during shadow mode.
- No hidden automatic provider switching.
- No universal-best-model leaderboard.
- No multi-agent fan-out without paired proof.
- No public product or billing until it is a daily-driver win locally.
- No custom subprocess/event-normalization layer while an existing runtime passes the fit contract.

## Decisive recommendation

Keep Claude on Flight Deck. The direct pinned Zed/Codex ACP boundary and the read-only Switchyard compiler now pass headlessly; do not add `acpx` merely to create another layer. Kruz's next move is the one-click Zed visual check, followed by twenty real shadow receipts. Build only the evidence/policy/receipt layer that existing runtimes lack. If that layer earns promotion, it can power Zed, Flight Deck, Orbit, and a terminal without betting the company on any one shell or rewriting commodity plumbing.
