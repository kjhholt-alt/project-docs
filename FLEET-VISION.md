# FLEET: Multi-Agent Claude Code Orchestration Platform

## Deep Research Report — March 25, 2026

> **Research scope**: Competitive landscape, technical feasibility, product gaps, architecture plan, and business model for building a "million dollar" multi-agent orchestration product on top of Claude Code's channels system.

---

## 1. EXECUTIVE SUMMARY

**The core insight**: Claude Code + Discord channels is already the best multi-agent coding setup that exists — but it's missing the orchestration layer that would make it a product. No tool today gives you: always-on AI coding agents + mobile access + full local filesystem access + a unified dashboard + process management + cost tracking. That's the gap.

**What exists today (Kruz's setup)**:
- 2 Claude Code instances running in Discord (Claude 1 in #claude-chat, Claude 2 in #CC-chat)
- Each with its own bot token, state directory, and channel routing
- Full filesystem, MCP, git access from Discord mobile
- Memory system persisting across sessions
- WezTerm 5-pane terminal layout
- Nexus prototype (swarm orchestrator + dashboard at nexus.buildkit.store)
- Admin dashboard at admin.buildkit.store
- n8n automation hub on Railway

**What's missing to make this a product**:
1. Unified real-time dashboard showing all agents
2. Process lifecycle management (start/stop/restart/health)
3. Cross-agent task coordination
4. Cost and token usage tracking
5. Agent templates and role management
6. One-click deployment for new agents

**Market timing**: Perfect. The multi-agent AI coding space exploded in early 2026 — VS Code added multi-agent orchestration, Composio's Agent Orchestrator hit 5.4k GitHub stars, and Claude Code just shipped Agent Teams, Channels, and Remote Control all in the same quarter.

---

## 2. COMPETITIVE LANDSCAPE

### 2.1 Direct Competitors

#### Composio Agent Orchestrator (5.4k stars)
- **What it does**: Manages fleets of parallel AI coding agents. Each agent gets its own git worktree, branch, and PR. Autonomous CI fix, review comment handling.
- **Architecture**: Plugin-based with 8 swappable layers (runtime, agent, workspace, tracker, notifier, terminal, SCM, lifecycle)
- **Dashboard**: Web UI at localhost:3000 with real-time monitoring
- **Supports**: Claude Code, Codex, Aider as agent backends
- **Limitations**: Requires tmux, no mobile access, no Discord integration, no always-on mode, no cost tracking, filesystem-based state only
- **Pricing**: Open source (MIT), self-hosted

#### Overstory (jayminwest)
- **What it does**: Multi-agent AI coding orchestration with hierarchical agent structure
- **Architecture**: Orchestrator → Coordinator → Supervisor → Workers, with SQLite mail system for inter-agent messaging
- **Dashboard**: TUI dashboard (terminal-based), not web
- **Supports**: 11 runtime adapters (Claude Code stable, plus Cursor, Copilot, Codex, etc.)
- **Key innovation**: Tiered watchdog system (mechanical daemon + AI triage + monitor agent)
- **Limitations**: TUI-only (no web/mobile), complex setup, no Discord/chat integration
- **Pricing**: Open source

#### Ruflo (ruvnet, 5.9k commits)
- **What it does**: Enterprise-grade multi-agent orchestration built on Claude Code
- **Architecture**: 6-layer system with Q-Learning router, 8 MoE experts, 60+ specialized agents
- **Swarm mode**: Queen-led hierarchy with consensus voting, anti-drift controls
- **Key innovation**: RuVector intelligence layer with self-learning, RL-based task routing (250% efficiency gain claimed)
- **Limitations**: Very complex, enterprise-focused, no mobile/Discord interface
- **Pricing**: Open source

#### VS Code Multi-Agent (Microsoft)
- **What it does**: Multi-agent orchestration built into VS Code 1.112+
- **Limitation**: IDE-locked, no mobile access, no always-on mode

### 2.2 Claude Code Native Features

| Feature | Status | What it does |
|---------|--------|-------------|
| **Channels** | Research preview (March 2026) | Push messages from Discord/Telegram/iMessage into running sessions |
| **Agent Teams** | Experimental | Coordinate multiple Claude Code instances with shared task list and messaging |
| **Subagents** | Stable | Spawn specialized helper agents within a session |
| **Remote Control** | Research preview (Feb 2026) | Drive local session from claude.ai or mobile app |
| **Scheduled Tasks** | Stable | Run agents on cron schedules (cloud-hosted) |
| **Agent SDK** | Stable | Run Claude Code programmatically from CLI/Python/TypeScript |
| **Git Worktrees** | Stable | Isolated parallel sessions per worktree |
| **Hooks** | Stable | Intercept tool calls, session events for automation |

### 2.3 The "5 Agents in Discord" Approach (artemxtech)
Someone already published a blog post doing what Kruz does — 5 Claude agents across 5 Discord channels with an Obsidian vault as the shared context layer. Their 3-layer stack: Obsidian (context) → Claude Code (executor) → Discord (interface). Key insight: "Full observability — agents' file edits remain visible in Obsidian."

### 2.4 Gap Analysis

| Capability | Agent Orchestrator | Overstory | Ruflo | VS Code | Claude Teams | **FLEET (ours)** |
|------------|-------------------|-----------|-------|---------|-------------|-----------------|
| Web dashboard | Yes (basic) | No (TUI) | No | No | No | **Yes** |
| Mobile access | No | No | No | No | Yes (Remote Control) | **Yes (Discord + Web)** |
| Always-on agents | No | No | No | No | No | **Yes** |
| Discord integration | No | No | No | No | No | **Yes** |
| Cost tracking | No | Yes (basic) | Yes | No | No | **Yes** |
| Agent marketplace | No | No | No | No | No | **Planned** |
| Process management | tmux | tmux | Internal | IDE | Internal | **Daemon** |
| Inter-agent comms | No | SQLite mail | Swarm | No | Mailbox | **Discord + Bus** |
| Claude Code native | Plugin | Adapter | Hook-based | N/A | Built-in | **Channels** |
| Open source | Yes | Yes | Yes | No | No | **Yes (core)** |

**The opportunity**: No tool combines always-on agents + mobile access + web dashboard + cost tracking + Discord interface. That's FLEET.

---

## 3. WHAT MAKES THIS A MILLION-DOLLAR PRODUCT

### 3.1 Market Size

- **AI coding assistant market**: Projected $15.3B by 2027 (MarketsandMarkets)
- **Claude Code Max subscribers**: Estimated 100K-500K at $200/month (Anthropic hasn't disclosed)
- **Developer productivity tools**: $7.2B market in 2025
- **Discord developer bots**: 50M+ servers with bots, 200M+ monthly active Discord users

### 3.2 Pricing Sweet Spot

| Competitor | Price |
|-----------|-------|
| Claude Code Max | $200/month |
| Cursor Pro | $20/month |
| GitHub Copilot Business | $19/month |
| Devin | $500/month |
| Replit Agent | $25/month |

**FLEET pricing**: $29-99/month on top of Claude Code Max
- People already paying $200/month for Claude Code WANT to maximize that investment
- If FLEET saves 10+ hours/month of agent management overhead, it's a no-brainer
- $29/month = Solo dev, 3 agents, basic dashboard
- $49/month = Pro, 10 agents, full dashboard, cost tracking, templates
- $99/month = Team, unlimited agents, shared dashboard, API access

### 3.3 Revenue Model

At 5,000 paying users (0.5-1% of Claude Max subscribers):
- Low tier: 2,500 x $29 = $72,500/month
- Mid tier: 2,000 x $49 = $98,000/month
- High tier: 500 x $99 = $49,500/month
- **Total: $220,000/month = $2.6M ARR**

### 3.4 Competitive Moats

1. **First mover in Discord + Claude Code orchestration** — Nobody else is building this specific combo
2. **Community-driven agent marketplace** — Templates, personas, skill packs that compound in value
3. **Deep Claude Code integration** — Using channels, hooks, and Agent SDK (official APIs, not hacks)
4. **Mobile-first design** — Discord native means phones are first-class citizens
5. **Open-source core** — Community builds adapters, templates; monetize dashboard + cloud features

---

## 4. PRODUCT ARCHITECTURE

### 4.1 System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      FLEET PLATFORM                         │
│                                                             │
│  ┌──────────┐  ┌──────────────┐  ┌───────────────────────┐ │
│  │ Fleet CLI │  │ Web Dashboard│  │  Discord Bot (Enhanced)│ │
│  │ fleet ...  │  │ fleet.app    │  │  /fleet status, etc.  │ │
│  └─────┬─────┘  └──────┬───────┘  └───────────┬───────────┘ │
│        │               │                       │             │
│  ┌─────▼───────────────▼───────────────────────▼──────────┐ │
│  │               FLEET CONTROL PLANE                       │ │
│  │                                                         │ │
│  │  ┌─────────────┐  ┌──────────────┐  ┌───────────────┐ │ │
│  │  │ Process Mgr │  │ Task Queue   │  │ Event Bus     │ │ │
│  │  │ (systemd/pm2)│  │ (SQLite/BQ)  │  │ (WebSocket)   │ │ │
│  │  └──────┬──────┘  └──────┬───────┘  └───────┬───────┘ │ │
│  │         │                │                   │         │ │
│  │  ┌──────▼────────────────▼───────────────────▼───────┐ │ │
│  │  │              AGENT RUNTIME LAYER                   │ │ │
│  │  │                                                    │ │ │
│  │  │  ┌─────────┐  ┌─────────┐  ┌─────────┐           │ │ │
│  │  │  │ Agent 1 │  │ Agent 2 │  │ Agent N │           │ │ │
│  │  │  │ claude  │  │ claude  │  │ claude  │           │ │ │
│  │  │  │ --chan.. │  │ --chan.. │  │ --chan.. │           │ │ │
│  │  │  │ Discord │  │ Discord │  │ Telegram│           │ │ │
│  │  │  └─────────┘  └─────────┘  └─────────┘           │ │ │
│  │  └────────────────────────────────────────────────────┘ │ │
│  │                                                         │ │
│  │  ┌──────────────────────────────────────────────────┐  │ │
│  │  │              OBSERVABILITY LAYER                  │  │ │
│  │  │  Token counter │ Cost tracker │ Git monitor       │  │ │
│  │  │  Error alerting │ Session logger │ Health probes  │  │ │
│  │  └──────────────────────────────────────────────────┘  │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │              DATA LAYER                                 │ │
│  │  Supabase: agents, sessions, tasks, costs, events       │ │
│  └─────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### 4.2 Core Components

#### A. Fleet Daemon (fleet-daemon)
The heart of the system. A Node.js/Bun process that manages Claude Code agent lifecycles.

**Responsibilities**:
- Start/stop/restart Claude Code processes
- Health monitoring (process alive, responsive, memory usage)
- Auto-restart on crash with exponential backoff
- Graceful shutdown with session save
- Resource limits (max agents, memory caps)

**Implementation approach**:
- Use the Claude Code Agent SDK (`@anthropic-ai/claude-code`) to spawn agents programmatically
- Each agent runs as a managed subprocess with `--channels`, `--agents`, `--permission-mode`
- Daemon listens on a Unix socket or local HTTP port for control commands
- State persisted to SQLite (lightweight, zero-dependency)

```typescript
// Pseudocode for agent spawning
import { spawn } from 'child_process';

interface AgentConfig {
  name: string;
  channel: 'discord' | 'telegram' | 'imessage';
  channelId: string;
  persona: string;
  tools: string[];
  model: string;
  workingDir: string;
  permissionMode: string;
}

function spawnAgent(config: AgentConfig) {
  const proc = spawn('claude', [
    '--channels', `plugin:${config.channel}@claude-plugins-official`,
    '--agents', JSON.stringify({
      [config.name]: {
        description: config.persona,
        prompt: loadPersona(config.name),
        tools: config.tools,
        model: config.model,
      }
    }),
    '--agent', config.name,
    '--permission-mode', config.permissionMode,
    '--name', config.name,
  ], {
    cwd: config.workingDir,
    env: {
      ...process.env,
      DISCORD_STATE_DIR: `~/.fleet/agents/${config.name}/discord`,
      DISCORD_BOT_TOKEN: config.botToken,
    }
  });

  return new AgentProcess(proc, config);
}
```

#### B. Fleet CLI (fleet)
A simple CLI for managing agents from the terminal.

```
fleet status                    # Show all agents and their status
fleet spawn <name> [--config]   # Start a new agent from config
fleet stop <name>               # Gracefully stop an agent
fleet restart <name>            # Restart an agent
fleet logs <name> [--tail]      # View agent session logs
fleet cost [--period 7d]        # Show cost breakdown by agent
fleet config                    # Edit fleet configuration
fleet dashboard                 # Open web dashboard
fleet agent list                # List available agent templates
fleet agent create <name>       # Create a new agent template
```

#### C. Web Dashboard (fleet-dashboard)
Next.js 16 app deployed to Vercel, connecting to Supabase for data and WebSocket for real-time updates.

**Key views**:
1. **Fleet Overview** — Grid of agent cards showing name, status (active/idle/blocked/error), current task, uptime, token usage
2. **Agent Detail** — Full session log, conversation history, file changes, cost breakdown, performance metrics
3. **Task Board** — Kanban-style task management across all agents
4. **Cost Center** — Token usage charts, cost-per-agent, daily/weekly/monthly trends, budget alerts
5. **Configuration** — Agent templates, channel routing, persona editor, tool permissions
6. **Activity Feed** — Real-time stream of all agent actions across the fleet

**Design**: Palantir/DoD aesthetic (per Kruz's preference) — dark navy-black (#080b12), cyan/emerald accents, monospace metrics, clean borders.

#### D. Enhanced Discord Bot
Extends the existing Discord setup with fleet management slash commands:

```
/fleet status              # Quick status of all agents
/fleet assign <agent> <task>  # Assign a task to an agent
/fleet cost                # Cost summary
/fleet pause <agent>       # Pause an agent
/fleet resume <agent>      # Resume an agent
```

Plus automatic status embeds:
- Agent starts → embed in #deploys
- Agent completes task → embed in #projects
- Agent errors → embed in #automations
- Cost threshold hit → DM to user

#### E. Agent Configuration System
YAML-based agent definitions stored in `~/.fleet/agents/`:

```yaml
# ~/.fleet/agents/backend-dev.yaml
name: backend-dev
description: Senior backend engineer specializing in Python/Django
channel: discord
discord:
  botToken: ${FLEET_BOT_TOKEN_1}
  stateDir: ~/.fleet/agents/backend-dev/discord
  channels:
    respond:
      - "1485421359113703505"  # #claude-chat
    monitor:
      - "1485491838944018504"  # #projects
model: opus
permissionMode: auto
workingDir: ~/Desktop/Projects
tools:
  - Bash
  - Read
  - Edit
  - Write
  - Grep
  - Glob
mcpServers:
  - railway
  - supabase
  - github
persona: |
  You are a senior backend engineer. You specialize in Python, Django,
  FastAPI, and database design. You write clean, well-tested code.
  You prefer simple solutions over complex ones.
schedule:
  healthCheck: "*/15 * * * *"  # Every 15 min
  dailyReport: "0 8 * * *"     # 8am daily
limits:
  maxTokensPerHour: 500000
  maxCostPerDay: 20.00
```

#### F. Observability Layer
Built on hooks — intercept every tool call and log it:

```typescript
// PostToolUse hook logs every action
{
  "hooks": {
    "PostToolUse": [{
      "hooks": [{
        "type": "command",
        "command": "fleet-log --agent $AGENT_NAME --tool $TOOL_NAME --input $TOOL_INPUT"
      }]
    }]
  }
}
```

Metrics captured:
- Token usage per turn (from stream-json output)
- Tool call frequency and duration
- Git commits, pushes, PRs created
- Files read/written/edited
- Errors and retries
- Session duration and idle time

### 4.3 Data Model (Supabase)

```sql
-- Core tables
CREATE TABLE agents (
  id UUID PRIMARY KEY,
  name TEXT UNIQUE NOT NULL,
  status TEXT NOT NULL DEFAULT 'stopped', -- running, idle, blocked, error, stopped
  config JSONB NOT NULL,
  pid INTEGER,
  session_id TEXT,
  started_at TIMESTAMPTZ,
  last_heartbeat TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE sessions (
  id UUID PRIMARY KEY,
  agent_id UUID REFERENCES agents(id),
  claude_session_id TEXT,
  started_at TIMESTAMPTZ NOT NULL,
  ended_at TIMESTAMPTZ,
  total_tokens INTEGER DEFAULT 0,
  estimated_cost DECIMAL(10,4) DEFAULT 0,
  tool_calls INTEGER DEFAULT 0,
  git_commits INTEGER DEFAULT 0,
  files_changed INTEGER DEFAULT 0
);

CREATE TABLE tasks (
  id UUID PRIMARY KEY,
  agent_id UUID REFERENCES agents(id),
  title TEXT NOT NULL,
  description TEXT,
  status TEXT NOT NULL DEFAULT 'pending', -- pending, in_progress, completed, failed
  priority INTEGER DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT now(),
  started_at TIMESTAMPTZ,
  completed_at TIMESTAMPTZ,
  result TEXT
);

CREATE TABLE events (
  id UUID PRIMARY KEY,
  agent_id UUID REFERENCES agents(id),
  session_id UUID REFERENCES sessions(id),
  event_type TEXT NOT NULL, -- tool_call, message, error, status_change, cost_update
  data JSONB NOT NULL,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE cost_tracking (
  id UUID PRIMARY KEY,
  agent_id UUID REFERENCES agents(id),
  period_start TIMESTAMPTZ NOT NULL,
  period_end TIMESTAMPTZ NOT NULL,
  input_tokens BIGINT DEFAULT 0,
  output_tokens BIGINT DEFAULT 0,
  estimated_cost DECIMAL(10,4) DEFAULT 0,
  model TEXT
);

-- Real-time subscriptions via Supabase Realtime
-- Dashboard subscribes to agents, events, and cost_tracking tables
```

---

## 5. WHAT'S STOPPING THIS FROM BEING PERFECT

### 5.1 Technical Barriers

| Barrier | Severity | Mitigation |
|---------|----------|------------|
| **Claude Code Channels is research preview** | HIGH | API may change. Build abstraction layer. But it works NOW and is official. |
| **No official token counting API** | MEDIUM | Parse stream-json output, estimate from model pricing. Claude Code may add this. |
| **Windows process management** | MEDIUM | Use pm2 or node-windows for daemon. Not as clean as systemd but works. |
| **Discord bot token limit** | LOW | One bot token per agent OR use channel routing with one bot. Both work. |
| **Permission prompts pause agents** | MEDIUM | Use `--permission-mode auto` or `--dangerously-skip-permissions` for trusted envs. |
| **Channels only allows Anthropic-approved plugins** | HIGH | Must use official Discord/Telegram plugins. Custom channels need `--dangerously-load-development-channels`. |
| **Agent Teams is experimental** | MEDIUM | Don't depend on it initially. Use our own coordination layer. |
| **Session persistence across restarts** | MEDIUM | Use `--session-id` and `--resume` to restore sessions. Store session IDs. |

### 5.2 Product Gaps to Fill

1. **Agent-to-Agent Communication**: Claude Code Teams has a mailbox but it's experimental. Build our own via Discord channels (Agent A posts to shared channel, Agent B reads it) or via a lightweight message bus (SQLite + polling).

2. **Cost Attribution**: Claude Code Max is flat-rate ($200/mo) so token tracking is for optimization, not billing. But if users are on API keys, cost tracking becomes critical. Track tokens from stream-json output.

3. **Conflict Prevention**: Two agents editing the same file is the #1 problem in multi-agent coding. Solutions:
   - Git worktrees (each agent gets isolated copy) — Claude Code supports this natively
   - File locking via hooks (PreToolUse checks if another agent holds the file)
   - Project-per-agent assignment (different agents own different repos)

4. **Onboarding UX**: Setup today requires: create Discord bot, install plugin, configure access, create second bot, configure second instance. This needs to be a one-command setup:
   ```
   fleet init --agents 3 --channel discord
   ```

5. **Scaling Beyond One Machine**: Currently all agents run on one machine. For teams:
   - Use Claude Code's scheduled tasks (cloud-hosted, no machine needed)
   - Use Remote Control + Channels together
   - Eventually: hosted fleet with managed infrastructure

### 5.3 What the Competitors Get Wrong

1. **Agent Orchestrator**: Great architecture, but no mobile access and no always-on mode. It's designed for "run a batch of agents on issues" not "have persistent coding assistants."

2. **Overstory**: TUI-only dashboard is a dealbreaker for mobile users. Their SQLite mail system is clever but adds complexity. Discord IS the message bus.

3. **Ruflo**: Over-engineered. 60+ agent types, Q-Learning routers, RL algorithms — most developers don't need this. They need 2-5 agents that work reliably.

4. **Claude Code Teams**: The best native option, but experimental, no persistence across restarts, and no web dashboard. Good for one-off parallel tasks, not always-on agents.

5. **Everyone**: Nobody has mobile-first design. Nobody uses Discord as the primary interface. Nobody tracks costs at the agent level.

---

## 6. IMPLEMENTATION PLAN

### Phase 1: Foundation (2-3 weeks)
**Goal**: Basic fleet management with dashboard

1. **Fleet daemon** — Node.js process manager for Claude Code instances
   - Start/stop/restart agents
   - Health monitoring with heartbeats
   - Auto-restart on crash
   - SQLite for local state
   - Unix socket for CLI communication

2. **Fleet CLI** — Basic commands
   - `fleet init` — Initialize fleet configuration
   - `fleet spawn <name>` — Start an agent
   - `fleet stop <name>` — Stop an agent
   - `fleet status` — Show all agents
   - `fleet logs <name>` — View logs

3. **Agent config system** — YAML-based agent definitions
   - Persona templates
   - Channel routing
   - Tool permissions
   - Working directory

4. **Minimal dashboard** — Next.js 16 + Supabase
   - Agent status cards
   - Real-time updates via Supabase Realtime
   - Dark Palantir theme

### Phase 2: Intelligence (2-3 weeks)
**Goal**: Cross-agent coordination and observability

1. **Task queue** — Shared task management
   - Create/assign/complete tasks
   - Dependencies between tasks
   - Agent self-claiming
   - Discord integration (post tasks, claim via reactions)

2. **Event bus** — Real-time event streaming
   - Tool call logging via hooks
   - Git activity monitoring
   - File change tracking
   - WebSocket feed to dashboard

3. **Cost tracking** — Token usage monitoring
   - Parse stream-json output for token counts
   - Per-agent cost breakdown
   - Daily/weekly trends
   - Budget alerts via Discord

4. **Enhanced Discord bot** — Slash commands and embeds
   - `/fleet status` — Status embed
   - `/fleet task <desc>` — Create and assign task
   - `/fleet cost` — Cost summary
   - Auto-posting to dedicated channels

### Phase 3: Polish & Ship (2-3 weeks)
**Goal**: Production-ready product

1. **Onboarding flow** — One-command setup
   - `fleet init` creates Discord bots, configures channels, sets up agents
   - Interactive CLI wizard
   - Pre-built agent templates (backend dev, frontend dev, reviewer, researcher)

2. **Dashboard v2** — Full-featured web app
   - Agent detail views with session history
   - Kanban task board
   - Cost analytics with charts
   - Configuration editor
   - Activity feed

3. **Agent marketplace** — Community templates
   - Share agent configurations
   - Skill packs (collections of CLAUDE.md instructions)
   - Channel routing presets

4. **Documentation & marketing**
   - Landing page
   - Setup guide
   - Video demo
   - Product Hunt launch

### Phase 4: Scale (ongoing)
- Team features (shared fleet across team members)
- Cloud-hosted agents (no local machine needed)
- API for programmatic fleet management
- Integrations (Linear, GitHub Issues, Jira for task sources)
- AI-powered task decomposition (describe a feature, fleet breaks it into agent tasks)

---

## 7. TECH STACK

| Component | Technology | Why |
|-----------|-----------|-----|
| Fleet daemon | Node.js/Bun + TypeScript | Same runtime as Claude Code, good process management |
| Fleet CLI | Commander.js + Ink | Rich terminal UI, familiar patterns |
| Dashboard | Next.js 16 + TypeScript | Vercel auto-deploy, shadcn/ui |
| UI components | shadcn/ui + Tailwind v4 | Palantir aesthetic, dark mode |
| Database | Supabase | Real-time subscriptions, auth, already used |
| Local state | SQLite (better-sqlite3) | Zero-dependency, fast, reliable |
| Process mgmt | pm2 or custom daemon | Cross-platform, auto-restart |
| Discord bot | discord.js 14 | Slash commands, embeds, reactions |
| Real-time | Supabase Realtime + WebSocket | Dashboard live updates |
| Charts | Recharts or Tremor | Cost analytics visualization |
| Deployment | Vercel (dashboard) + npm (CLI) | Easy distribution |

---

## 8. NAMING & BRANDING

**Primary name**: FLEET
- Short, memorable, conveys "managing a fleet of agents"
- `fleet` CLI feels natural
- Alternatives considered: CommandPost, Nexus Fleet, Terminal, Swarm, Hive

**Tagline options**:
- "Your AI coding fleet, always on"
- "Orchestrate your AI agents from anywhere"
- "Multiple Claude Code agents. One command center."

**Domain**: Check availability for fleet.dev, fleetcode.dev, getfleet.dev, usefleet.dev

---

## 9. RISKS & MITIGATIONS

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| Anthropic ships native orchestration that obsoletes FLEET | HIGH | MEDIUM | Build value above Claude Code (dashboard, cost tracking, templates). Even if Anthropic adds orchestration, they won't build a Discord+web dashboard product. |
| Channels API changes in breaking ways | MEDIUM | LOW | Abstract channel interface. Channels is the future of Claude Code's plugin system — Anthropic is committed. |
| Market too small (not enough Claude Max users) | HIGH | LOW | Market is growing fast. Claude Code usage tripled in Q1 2026. |
| Competitor launches similar product | MEDIUM | MEDIUM | First-mover advantage + community + open-source core. Ship fast. |
| Claude Code Max pricing changes | MEDIUM | LOW | FLEET adds value regardless of Claude's pricing model. |
| Too complex for solo developers | MEDIUM | MEDIUM | Default to simple setup (1-2 agents). Complexity is opt-in. |

---

## 10. IMMEDIATE NEXT STEPS (THIS WEEK)

1. **Validate the daemon approach** — Can we reliably spawn and manage Claude Code processes with the Agent SDK? Build a proof-of-concept that starts 2 agents, monitors health, auto-restarts on crash.

2. **Build minimal CLI** — `fleet init`, `fleet spawn`, `fleet stop`, `fleet status`. Just enough to prove the workflow.

3. **Create GitHub repo** — `kjhholt-alt/fleet` (or `fleet-orchestrator`). MIT license. README with vision doc.

4. **Design dashboard wireframes** — Palantir aesthetic, 5 key views. Use v0 to generate initial components.

5. **Test with existing setup** — Can the daemon manage Kruz's existing 2-agent Discord setup? Migrate from manual `claude --channels` to daemon-managed.

6. **Research Claude Code Agent SDK** — The TypeScript SDK (`@anthropic-ai/claude-code`) is the cleanest way to spawn agents programmatically. Understand its API surface.

---

## 11. THE BOTTOM LINE

The multi-agent AI coding market is in its "Netscape moment." Everyone knows this is the future, but nobody has built the product that makes it accessible and manageable. The tools that exist (Agent Orchestrator, Overstory, Ruflo) are engineer-built-for-engineers — powerful but complex, terminal-only, no mobile access, no cost tracking, no marketplace.

FLEET's unfair advantage is **Discord**. 200M+ users already have it. It works on mobile. It has notifications, history, rich embeds, reactions, threads. Claude Code's channels system already bridges the gap between Discord and your local filesystem. FLEET just adds the orchestration, observability, and UX layer on top.

**The product that wins this market will be the one that makes multi-agent coding feel like managing a Slack workspace, not configuring a Kubernetes cluster.** FLEET is that product.

---

*Research compiled by Claude 2 (CC-chat) on March 25, 2026. Sources: Claude Code official docs, Composio Agent Orchestrator GitHub, Overstory GitHub, Ruflo GitHub, artemxtech Substack, VS Code release notes, Anthropic documentation, market research from various sources.*
