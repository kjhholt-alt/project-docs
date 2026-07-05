# Always-On Personal AI Assistant for the MS-A2 — Research Report

*Prepared 2026-07-04 · cloud research lane · web-sourced, cited. Companion: [ASSISTANT-STACK-INVENTORY.md](ASSISTANT-STACK-INVENTORY.md) (what we already have) and [ASSISTANT-V1-PLAN.md](ASSISTANT-V1-PLAN.md) (the synthesis).*

---

## 1. OpenClaw deep-dive

### What it is and where it stands (July 2026)

OpenClaw is Peter Steinberger's open-source "personal AI assistant you run on your own devices." First published November 2025 (as Warelay/Clawdbot), renamed **Moltbot** on 2026-01-27 after Anthropic trademark complaints, then **OpenClaw** three days later ([Wikipedia](https://en.wikipedia.org/wiki/OpenClaw)). It went hyper-viral: ~247k GitHub stars by March 2026, **~382k stars / 80k forks today** ([repo](https://github.com/openclaw/openclaw)). Steinberger joined OpenAI on 2026-02-14 and handed stewardship to a non-profit **OpenClaw Foundation**, with OpenAI as primary subscription sponsor ([TechCrunch](https://techcrunch.com/2026/02/15/openclaw-creator-peter-steinberger-joins-openai/), [Forbes](https://www.forbes.com/sites/ronschmelzer/2026/02/16/openai-hires-openclaw-creator-peter-steinberger-and-sets-up-foundation/)). Through Q2 2026 the core team still ships rapidly and community contributions accelerated under the foundation ([Releasebot](https://releasebot.io/updates/openclaw), [release policy](https://docs.openclaw.ai/reference/RELEASING)). Velocity has a quality cost: releases are rolling `vYYYY.M.D` with stable/beta/dev channels, and some stables regress badly — community tracker [ClawStat.us](https://clawstat.us/) flagged v2026.6.11 as "skip this version" (empty tool output, memory_search index races, packaging flaw). **License: MIT.**

### Architecture

Hub-and-spoke around a single long-lived **Gateway daemon** on `ws://127.0.0.1:18789` — traffic controller and source of truth, typed WebSocket API, JSON-schema-validated frames (`connect` must be first frame or hard-close) ([innFactory architecture deep-dive](https://innfactory.ai/en/blog/openclaw-architecture-explained/)):

- **Channel adapters** — 24+ platforms: WhatsApp (via Baileys, the *unofficial* WhatsApp Web protocol — account-ban risk), Telegram (grammY), Discord, Slack, Signal, iMessage, Teams, Matrix, IRC, WeChat, etc. ([repo](https://github.com/openclaw/openclaw))
- **Agent runtime** — the reasoning loop; executes shell, files, browser (Puppeteer); multi-agent routing to isolated agents with separate workspaces
- **Sessions** — default is one shared `main` DM session per agent (all DMs share context!); opt-in "secure DM mode" isolates per-conversation; JSONL persistence in `~/.openclaw/agents/<id>/sessions/`
- **Memory** — plain Markdown: `MEMORY.md` (durable, loaded at bootstrap), `memory/YYYY-MM-DD.md` daily notes (today+yesterday auto-loaded), optional `DREAMS.md` consolidation; hybrid vector+keyword search on SQLite; **OpenAI embeddings by default**; a "memory flush" background turn saves context before compaction. Notable doc admission: *"memory can preserve approval context, but it does not enforce policy"* ([memory docs](https://docs.openclaw.ai/concepts/memory)). This is essentially a convergent evolution of Kruz's own MEMORY.md + Atlas pattern — and REM's dream consolidation predates their DREAMS.md.
- **Skills/plugins** — hot-reloadable skills + the **ClawHub** marketplace (see security), first-class cron, webhooks, heartbeats; **Live Canvas** (agent-driven visual workspace), voice wake/talk on macOS/iOS/Android.

### Model backends and Claude auth — the sharp edge

- Any provider via an abstraction layer (OpenAI, Anthropic, Ollama/local, etc.), with failover and profile rotation. OpenAI is the sponsor; docs steer toward "a flagship model from the provider you trust."
- **Anthropic enforced a ban on third-party harnesses using Claude subscription OAuth on 2026-04-04** — subscription tokens are restricted to official surfaces (Claude.ai, Claude Code, Claude Desktop); third-party tools must use API keys ([MindStudio](https://www.mindstudio.ai/blog/anthropic-openclaw-ban-oauth-authentication), [dev.to](https://dev.to/mcrolly/anthropic-kills-claude-subscription-access-for-third-party-tools-like-openclaw-what-it-means-for-3ipc)).
- OpenClaw's current sanctioned Claude paths: (a) **Anthropic API key** (pay-as-you-go, recommended), or (b) **"Claude CLI" mode** — shells into an existing Claude Code login on the same host, drawing from the subscription's usage limits ([OpenClaw Anthropic docs](https://docs.openclaw.ai/providers/anthropic)). Anthropic's own help center confirms Agent SDK / `claude -p` under Pro/Max is allowed and currently counts against normal plan limits (the announced separate Agent-SDK credit was **paused 2026-06-15**) ([support.claude.com](https://support.claude.com/en/articles/15036540-use-the-claude-agent-sdk-with-your-claude-plan)). Net: an OpenClaw-on-Claude runtime either costs real API dollars or **competes with the existing Claude Code fleet for the same subscription budget**.

### Windows support — verified current story

Better than its Mac/Linux-first reputation, but still tiered ([official Windows docs](https://docs.openclaw.ai/platforms/windows)):

1. **Windows Hub** (recommended) — native WinUI companion app, signed installers, tray, chat, node mode (canvas/screen/camera/notifications), local MCP mode.
2. **Native Windows CLI/Gateway** — `iwr -useb https://openclaw.ai/install.ps1 | iex`; runs via **Scheduled Tasks with a `gateway.vbs` wrapper to hide console windows** (identical to our headless-spawn doctrine, amusingly).
3. **WSL2** — still what docs call "the most Linux-compatible Gateway runtime"; needs systemd enabled; WSL IP drift breaks LAN forwarding after restarts; community guides overwhelmingly still route through WSL2 ([Stack Junkie](https://www.stack-junkie.com/blog/openclaw-windows-setup-guide), [gist](https://gist.github.com/Jany-M/abd4ddad428d03bc40123b4da7ac3d10)).

Requirements: Node 24 (or 22.19+), `npm i -g openclaw@latest`, `openclaw onboard --install-daemon`. Footprint is modest (one Node daemon + state in `~/.openclaw`); iMessage needs a Mac; voice wake is macOS/iOS/Android — Windows gets Hub features, not the full voice stack.

### Security posture and incidents (the big caveat)

OpenClaw is the canonical **lethal trifecta** machine — private data + untrusted inbound content + ability to act/exfiltrate ([Simon Willison](https://simonwillison.net/tags/lethal-trifecta/), [Fortune](https://fortune.com/2026/01/31/ai-agent-moltbot-clawdbot-openclaw-data-privacy-security-nightmare-moltbook-social-network/)). The Jan–Feb 2026 record:

- **Mass exposure**: Censys tracked ~1,000 → 21,000+ publicly exposed gateways in one week (Jan 25–31); researcher Maor Dayan found **42,665 exposed instances, 93.4% of verified-vulnerable ones with auth bypass** — largely a default that trusted anything from localhost, fatal behind misconfigured reverse proxies ([Conscia](https://conscia.com/blog/the-openclaw-security-crisis/), [Infosecurity](https://www.infosecurity-magazine.com/news/researchers-six-new-openclaw/), [Giskard](https://www.giskard.ai/knowledge/openclaw-security-vulnerabilities-include-data-leakage-and-prompt-injection-risks)).
- **CVE-2026-25253** (CVSS 8.8): 1-click RCE — Control UI blindly trusted a `gatewayUrl` query param and auto-connected, exfiltrating the auth token; patched in v2026.1.29 ([The Hacker News](https://thehackernews.com/2026/02/openclaw-bug-enables-one-click-remote.html), [NVD](https://nvd.nist.gov/vuln/detail/CVE-2026-25253), [runZero](https://www.runzero.com/blog/openclaw/)).
- **ClawHub supply-chain poisoning ("ClawHavoc")**: 341 malicious skills found initially (~12% of the registry), later scans **800+ (~20%)**, mostly delivering the AMOS infostealer ([Foresiet](https://foresiet.com/blog/cve-2026-25253-openclaw-rce-fix/), [Kaspersky](https://www.kaspersky.com/blog/openclaw-vulnerabilities-exposed/55263/)).
- Academic treatment: [A Security Analysis of the OpenClaw AI Agent Framework (arXiv)](https://arxiv.org/pdf/2603.27517).

**Mitigations since shipped**: loopback bind default, gateway auth tokens, DM `pairing` policy ("treat inbound DMs as untrusted input" is now in the README), sandbox modes (`non-main`/`all`, Docker/SSH backends), tool allowlists per agent, `openclaw security audit` + `openclaw doctor`, exposure runbook ([repo](https://github.com/openclaw/openclaw), [Nebius hardening guide](https://nebius.com/blog/posts/openclaw-security)). The posture is much better than January, but the project's DNA is capability-first, and prompt injection remains unsolved by design.

---

## 2. The landscape beyond OpenClaw (2026)

| Option | What it is | Out-of-the-box value | Build-it-yourself share | Windows story |
|---|---|---|---|---|
| **OpenClaw** | Full assistant product (gateway, 24+ channels, memory, skills, cron, voice, canvas) | Highest — a finished PA | Config + hardening only | Native Hub + PS install; WSL2 still smoothest |
| **Claude Code headless / Agent SDK** | The Claude Code agent loop drivable from your own daemon: tools, permission modes, subagents, MCP, sessions ([Agent SDK docs](https://code.claude.com/docs/en/agent-sdk/overview)) | Agent loop, permissions, tools = free; **no channels, no memory service, no scheduler** — you own those | You write the daemon shell (we already have ~all of it) | First-class native Windows |
| **NanoClaw** ([nanoclaw.dev](https://nanoclaw.dev/), [GitHub](https://github.com/nanocoai/nanoclaw)) | "Lightweight OpenClaw" **built directly on the Claude Agent SDK**: ~15 source files / ~500 core lines, one Node process, per-group **Docker container isolation**, WhatsApp/Telegram/Discord/Slack/iMessage/email channels installed on demand, memory + scheduled jobs | Channel adapters + container sandbox on a Claude-native runtime | Small codebase, explicitly designed to be read and gutted | Needs Docker Desktop/WSL2 for isolation |
| **Hermes Agent** (Nous Research — *no relation to our `hermes/` broker*) | Autonomous agent, 111k stars, self-improving persistent skills, Telegram/Discord/Slack/WhatsApp/Signal out of the box, OpenClaw migration path ([Composio](https://composio.dev/content/openclaw-alternatives), [DeepInfra](https://deepinfra.com/blog/openclaw-alternatives)) | High, OpenClaw-like | Config-level | Linux-server-first |
| **ZeroClaw** | 3.4MB Rust binary, <5MB idle RAM, model-agnostic | Minimal assistant core, tiny footprint | Channels/skills thinner | Rust binary — portable |
| **IronClaw** (NEAR AI) | Security-first Rust rewrite; every untrusted tool in a **WASM capability sandbox** | Strongest sandbox story | Younger ecosystem | Rust — decent |
| **NemoClaw** (NVIDIA) | Hardened OpenClaw reference stack with OS-level sandboxing | Enterprise-y OpenClaw | Ops-heavy | Linux |
| **LangGraph / CrewAI** | Orchestration frameworks, not assistants — no channels, no daemon, no memory-across-channels product | Low for a PA; they're the *engine room*, you build the whole ship | Nearly everything | Fine (Python) |
| **Home-grown bot + LLM + cron** | Discord/Telegram bot polls → spawns `claude -p` → posts results; scheduler for proactivity | Zero out of box; total control, minimal surface | Everything — **which we have already built** | Perfect |

Key structural insight: the market split into (a) *assistant products* (OpenClaw, Hermes Agent) that bring channels/memory/cron but a second runtime and big attack surface, (b) *Claude-native minimal harnesses* (NanoClaw) that reuse the Agent SDK, and (c) *DIY daemon* patterns — where our existing stack is already a mature instance, with an approval-gate layer (greenlight) that none of the off-the-shelf options actually enforce.

---

## 3. Capability checklist — OpenClaw vs our Claude-Code-native stack

| Capability | OpenClaw | Our stack today |
|---|---|---|
| Persistent identity across channels | ● Strong — one agent, 24+ channels, shared workspace | ◐ Discord only; identity via CLAUDE.md/MEMORY.md is strong but single-channel |
| Memory (episodic + semantic) | ● MEMORY.md + daily notes + SQLite hybrid search (needs embeddings provider; default OpenAI) | ● Equivalent or better: MEMORY.md corpus + REM consolidation + Atlas SQLite graph + atlas_recall |
| Proactivity (schedules/triggers/feeds) | ● Cron, webhooks, heartbeats first-class per agent | ● 100+ scheduled flows, hermes flows, briefs, radars — more proven, less unified |
| Tool execution w/ permission tiers | ◐ Tool allowlists + sandbox modes; *approval is advisory, not enforced* | ● **Greenlight is a real enforced gate** (propose → !ok → dispatch → verify, standing approvals for safe classes) — ahead of OpenClaw |
| Escalation to human | ◐ Messages you on channel; no structured gate queue | ● #decision-queue + PR gates + Discord pings — structured |
| Multi-device reach (phone/voice) | ● Best-in-class: WhatsApp/Telegram/Signal/iMessage, mobile voice wake, Canvas | ◔ Discord app on phone only; Whisper dictation is local-desktop |
| Uptime / watchdog / self-heal | ◐ Daemon via launchd/systemd/Scheduled Tasks; no rich self-heal | ● task-scheduler-watch, runtime-clone self-heal, job-health control plane |
| Security (auth, injection defense, blast radius) | ◐ Much improved post-crisis, but capability-first history, ClawHub risk, huge default surface | ● Smaller surface: Discord-only inbound, allowlisted users, no public ports, secrets resolver; injection defense = same unsolved problem, but fewer inbound vectors |

**Score**: OpenClaw wins *channel reach + voice + polish*. Our stack wins *enforced approvals, watchdog maturity, memory governance, and blast-radius control*. The real gap is **reach (non-Discord channels, phone voice) and a single unified daemon identity** — not agent capability.

---

## 4. Build-vs-adopt — **verdict: BUILD-ON-OWN, ADAPT named pieces. Do not ADOPT.**

What adopting OpenClaw would genuinely add: WhatsApp/Telegram/Signal reach, mobile voice wake, Canvas, pairing UX, a unified always-on "one assistant" feel. What it costs:

1. **A second agent runtime beside Claude Code.** On Claude it's either API dollars or the Claude-CLI-reuse path that drains the same subscription limits the fleet already lives on. On GPT it forks the stack's brain. Every skill, memory convention, and permission rule would exist twice.
2. **Security surface.** OpenClaw's incident history is the worst of any tool in this class this year; even hardened, it adds a WebSocket control plane, a Node daemon with host exec, 24 channel adapters, and a marketplace that was ~20% malware. Our revenue-DB and PII rails make this a bad trade.
3. **Redundancy.** Bridge ≈ gateway+Discord adapter. Greenlight > OpenClaw approvals (enforced vs advisory). Hermes flows + 100 tasks ≈ cron/heartbeats. Atlas + MEMORY.md + REM ≈ (arguably >) OpenClaw memory. Whisper/murmur ≈ desktop voice. **We already own ~80% of OpenClaw's value with ~20% of its attack surface.**
4. **Maintenance.** Rolling releases with known-bad stables means a patch-fast-or-stay-vulnerable treadmill on a box whose whole point is unattended trust.

**ADAPT — borrow these named pieces:**
- **Telegram reach**: add a Telegram adapter to the existing bridge (grammY / python-telegram-bot; ~a day; pattern proven with Discord). Official API, ban-safe. **WhatsApp via Baileys is unofficial and risks the personal number — skip.**
- **NanoClaw as a parts bin**: MIT, ~15 files, Claude Agent SDK native — steal channel adapters + per-group container isolation pattern rather than running OpenClaw.
- **OpenClaw conventions worth copying**: memory-flush-before-compaction; daily `memory/YYYY-MM-DD.md` working notes; DM pairing codes for any new channel; per-session isolation for non-main channels; heartbeat events; `security audit`-style self-check command for the assistant daemon.

---

## 5. Security — 2026 best practice for an always-on, tool-wielding assistant at home

1. **Network: Tailscale-only, zero public ports.** Bind to loopback; reach remotely via tailnet or SSH tunnel. Never port-forward — that exact pattern created the 42k-instance exposure.
2. **Channel auth: allowlist by user ID, pair anything new.** One-time pairing codes for new devices/senders; treat all inbound DMs as untrusted input.
3. **Lethal trifecta management: cap one leg per session.** Sessions reading untrusted content get no exfil-capable tools and no secrets; sessions with secrets/repo write get no untrusted content. Structural, not prompt-based.
4. **Approval gates enforced in code, not memory.** Greenlight is already the right shape; the daemon must be *unable* to skip the gate.
5. **Secrets isolation.** Keep the secrets resolver; per-task scoped env injection; no secrets in memory files; rotate bot tokens onto the new box.
6. **Blast radius.** Non-admin user; heavy/untrusted work sandboxed; MS-A2 gets Tailscale ACL-scoped access to the main PC, not full SMB/admin; ops DBs read-only.
7. **Third-party skills: default no.** ClawHub's ~20% malware rate is the cautionary tale.
8. **Audit logging + watchdog.** Append-only log of every command/approval/tool call/denial; daily digest to Discord; alert on silence — never fake freshness.
9. **Patch policy.** Pin versions; never auto-update an agent daemon.

---

## 6. One-page verdict — what to run on the MS-A2 as v1

**Identity:** the MS-A2 becomes the *assistant box* — always-on daemon host for the stack we already trust. No new agent runtime.

**v1 = our own stack, promoted to a resident assistant:** Discord bridge + greenlight + hermes flows + Atlas + MEMORY.md corpus, migrated/replicated to the MS-A2, wrapped in one supervised "assistant daemon" identity, Tailscale-only.

**Build order:**
1. **Base hardening (day 1):** Windows 11 Pro, non-admin service account, Tailscale joined + ACLs, no inbound ports, sleep disabled, BitLocker.
2. **Port the runtime (days 1–2):** runtime clone + bridge + greenlight + scheduled flows; secrets via resolver; new bot tokens; verify `claude -p` headless auth.
3. **Watchdog (day 2):** task-scheduler-watch + heartbeat-to-Discord; silence alert; auto-restart.
4. **Assistant loop (days 3–5):** persistent conversational session identity on the bridge (session-per-channel, memory flush before compaction — borrow OpenClaw's conventions); briefs + alerts route from the new box.
5. **Phone reach (week 2):** Telegram adapter on our own bridge (official API). Voice later: Whisper piped into the bridge, not a new product.
6. **Only if multi-channel hunger is real (week 3+):** evaluate **NanoClaw** as a parts bin / thin channel front-end feeding greenlight — never as an autonomous second brain.

**Do NOT:**
- Adopt OpenClaw wholesale — second runtime, cost fork, worst security track record in class, near-zero net capability gain over what's built.
- Expose any gateway/UI publicly, ever.
- Wire WhatsApp via unofficial Baileys on the personal number.
- Install marketplace skills.
- Let any session hold all three trifecta legs; let standing approvals creep into irreversible classes.
- Auto-update the daemon; pin and review.

*(Full source list preserved in the research lane's transcript; primary links inline above.)*
