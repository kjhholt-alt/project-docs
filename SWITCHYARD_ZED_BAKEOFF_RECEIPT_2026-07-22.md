# Switchyard + Zed Bakeoff Receipt

Date: 2026-07-22

Posture: local, private, read-only, no popup, no dispatch

## Verdict

**KEEP Zed as a trial surface. KEEP Switchyard in shadow mode. SKIP a custom editor and skip `acpx` for now.**

The direct official Codex ACP adapter clears the machine boundary. The routing evidence does not clear live model selection, and Switchyard correctly returns `HOLD` instead of inventing confidence.

## Installed surface

- Zed 1.12.0 installed through Winget; application was not launched or made default.
- `@agentclientprotocol/codex-acp` 1.1.7 installed exact under `C:\Users\Kruz\.operator\switchyard\codex-acp`.
- Bundled compatible `@openai/codex` resolved exact to 0.145.0, matching the existing Codex CLI.
- Project-local Zed server: `switchyard-codex-readonly`.
- Environment contract: `INITIAL_AGENT_MODE=read-only`; no API key; logs confined to `~/.operator/switchyard/logs`.
- Zed's official contract is that External Agents own their runtime/auth/config, while Terminal Threads preserve the native CLI/TUI and its subscriptions/config. Sources: <https://zed.dev/docs/ai/external-agents>, <https://zed.dev/docs/ai/terminal-threads>.

## Machine proof

The local smoke client performed the ACP v1 newline-delimited JSON-RPC lifecycle documented at <https://agentclientprotocol.com/protocol/v1/initialization> and <https://agentclientprotocol.com/protocol/v1/transports>:

1. Started only the pinned adapter as a hidden child process.
2. Initialized ACP protocol version 1.
3. Advertised filesystem read `true`, filesystem write `false`, and terminal `false` from the client.
4. Created a session in the isolated implementation worktree.
5. Verified the adapter reported `currentModeId: read-only` and modes `read-only`, `agent`, `agent-full-access`.
6. Closed the exact active session and terminated the exact child process.

The adapter identified itself as Codex / `@agentclientprotocol/codex-acp` 1.1.7. It advertised ChatGPT-compatible auth choices, session load/resume/list/close/delete support, image/embedded-context prompts, and HTTP MCP support. No prompt turn, model call, tool call, file write, terminal request, or provider spend occurred.

## Switchyard proof

Implementation surfaces:

- `switchyard/core.py` — pure evidence reconciliation and signed route receipt.
- `switchyard/render.py` — self-contained human-readable HTML.
- `switchyard/acp_smoke.py` — bounded ACP initialize/session/close proof.
- `switchyard/.zed/settings.json` — project-scoped pinned read-only adapter.
- `switchyard/OPEN_SWITCHYARD_LAB.cmd` — refresh-and-open handoff Kruz can double-click.
- `switchyard/README.md`, `STATUS.md`, and nested `AGENTS.md` — durable operation and safety contract.

The live route used the existing Workflow Compiler rather than creating a second task classifier. It reconciled:

- Greenlight `design-L`: recommends Haiku from one observation; routing file is stale.
- Model Draft Room `design-l`: selects Claude Sonnet 4.6/high, one exact observation per lane, solo formation, public efficacy `hold-unreplicated`.
- Hermes Director: 100/100 L4, safe-local clear, zero human gates.
- Headroom state/flight: fresh and not in game mode.

Result: `shadow-hold`, unresolved model, `HOLD` harness, solo formation, execution disabled. Hold codes:

- `greenlight_stale`
- `greenlight_sparse`
- `draft_sparse`
- `draft_unreplicated`
- `evidence_conflict`

## Verification

- Sixteen `unittest` cases passed.
- Ruff passed.
- mypy passed all seven source files.
- compileall passed.
- Live JSON and HTML receipts generated under `~/.operator/switchyard`.
- HTML escaping/injection regression passed.
- Zed CLI help returned successfully without launching the editor.
- Real ACP v1 read-only session handshake and close passed.
- Git staged-diff whitespace check passed.
- Secret/full-access/trust-flag source scan returned no matches.

## Honest open gates

- The in-app Browser exposed no browser instance; no screenshot/click claim is made.
- Zed stayed closed to honor the no-popup workflow; Kruz's first double-click is the real visual/interaction proof.
- Native Codex and Claude Terminal Threads still need that visible Zed check.
- Greenlight policy freshness and route sample depth block live Switchyard promotion.
- No Flight Deck code changed. Its existing VS Code lane remains independent.

## Next move

Kruz opens `OPEN SWITCHYARD LAB.cmd`, verifies the Zed Agentic layout plus one Codex External Agent and one native Terminal Thread, then uses Switchyard shadow receipts on twenty real missions. Only verified outcome data can promote a live route through Greenlight/Hermes.
