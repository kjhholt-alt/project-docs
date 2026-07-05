# Claude Command Center — Architecture & Reuse Plan

## Personal daily driver for managing multiple Claude Code instances in Discord

---

## 1. THE REAL-TIME VISIBILITY SOLUTION

### Three layers of observability, each giving different granularity:

**Layer 1: Discord Message Monitor (conversation-level)**
A discord.js process that reads all messages from Claude channels in real-time.
- Listens to `#claude-chat` (Claude 1) and `#CC-chat` (Claude 2)
- Captures what the Claudes are saying, what they're asked to do
- Streams messages to Supabase `discord_messages` table
- Dashboard shows conversation feed per agent
- Also handles SENDING messages — type in the dashboard, it posts to the right Discord channel

**Layer 2: Claude Code Hooks (tool-level)**
PostToolUse hooks on both Claude instances capture every action.
- Every file read, edit, write, bash command, grep, glob
- Hooks POST to a local collector server OR directly to Supabase
- Dashboard shows real-time "Agent 1 is editing src/auth.py" with tool icons
- This is the `HookEventFeed` and `AgentSwimLane` components from Jarvis

**Layer 3: Session Transcript Tailing (full detail)**
Watch the JSONL session files for the deepest level of detail.
- `~/.claude/projects/<cwd>/<session-id>.jsonl` has EVERYTHING
- Token counts, full prompts, full responses, tool inputs/outputs
- Tail these files for the most granular view
- Use for historical analysis and cost calculation

### Architecture:

```
┌─────────────────────────────────────────────────────────────┐
│                    DASHBOARD (Next.js 16)                    │
│  AgentCards │ SwimLanes │ DiscordFeed │ CostTracker │ Input │
└─────────┬──────────────┬──────────────┬─────────────────────┘
          │              │              │
          ▼              ▼              ▼
┌─────────────────────────────────────────────────────────────┐
│                  SUPABASE (Real-time)                        │
│  discord_messages │ hook_events │ agent_sessions │ costs     │
└─────────┬──────────────┬──────────────┬─────────────────────┘
          │              │              │
    ┌─────▼─────┐  ┌─────▼─────┐  ┌───▼────────────┐
    │ Discord   │  │ Hook      │  │ Session File   │
    │ Monitor   │  │ Collector │  │ Watcher        │
    │ (bot)     │  │ (HTTP)    │  │ (chokidar)     │
    └─────┬─────┘  └─────┬─────┘  └───┬────────────┘
          │              │              │
    ┌─────▼─────┐  ┌─────▼─────┐  ┌───▼────────────┐
    │ Discord   │  │ Claude 1  │  │ Session JSONL  │
    │ Channels  │  │ Claude 2  │  │ files          │
    │ (API)     │  │ (hooks)   │  │ (~/.claude/)   │
    └───────────┘  └───────────┘  └────────────────┘
```

---

## 2. REUSABLE COMPONENTS INVENTORY

### From Jarvis Dashboard (jarvis-dashboard/) — BEST SOURCE

| Component | File | Reuse | Notes |
|-----------|------|-------|-------|
| **JarvisAgent type** | `src/lib/jarvis-types.ts` | KEEP | Perfect agent state model |
| **HookEvent type** | `src/lib/jarvis-types.ts` | KEEP | Full tool call event model |
| **Zustand store** | `src/lib/jarvis-store.ts` | KEEP | Global state: agents, events, costs |
| **WebSocket collector hook** | `src/hooks/useCollectorEvents.ts` | KEEP | Auto-reconnect, fallback polling |
| **AgentSummaryCard** | `src/components/dashboard/AgentSummaryCard.tsx` | KEEP | Agent status card |
| **AgentSwimLane** | `src/components/dashboard/AgentSwimLane.tsx` | KEEP | Per-agent tool timeline |
| **HookEventFeed** | `src/components/dashboard/HookEventFeed.tsx` | KEEP | Global event stream |
| **TranscriptViewer** | `src/components/dashboard/TranscriptViewer.tsx` | KEEP | Session history |
| **SessionBrowser** | `src/components/dashboard/SessionBrowser.tsx` | KEEP | Search past sessions |
| **CostTracker** | `src/components/dashboard/CostTracker.tsx` | KEEP | Per-project cost bars |
| **CostBreakdown** | `src/components/dashboard/CostBreakdown.tsx` | KEEP | Detailed cost view |
| **PerAgentNotes** | `src/components/dashboard/PerAgentNotes.tsx` | KEEP | Notes per agent |
| **JarvisTerminalGrid** | `src/components/dashboard/JarvisTerminalGrid.tsx` | ADAPT | xterm.js terminals (may not need) |
| **Claude pricing** | `src/lib/claude-pricing.ts` | KEEP | Token cost calculation |
| **Supabase client** | `src/lib/supabase.ts` | KEEP | DB connection |
| **Project presets** | `src/lib/jarvis.config.ts` | ADAPT | Add Kruz's projects |
| **Collector server** | `collector/server.ts` | BUILD | Empty — needs implementation |
| **Transcript parser** | `collector/transcript-parser.ts` | BUILD | Empty — needs implementation |

### From Nexus (nexus/) — SUPPLEMENTARY

| Component | File | Reuse | Notes |
|-----------|------|-------|-------|
| **Swarm orchestrator** | `swarm/orchestrator.py` | ADAPT | Task management + worker lifecycle |
| **Task manager** | `swarm/tasks/task_manager.py` | ADAPT | Supabase task CRUD + atomic claims |
| **Discord reporter** | `swarm/discord_reporter.py` | KEEP | Webhook embeds to Discord channels |
| **Worker types** | `swarm/workers/*.py` | REFERENCE | Patterns for worker tiers |
| **Memory system** | `swarm/memory.py` | KEEP | Cross-project shared memory |
| **Budget manager** | `swarm/budget/budget_manager.py` | ADAPT | Cost tracking per task |
| **Worktree isolation** | `swarm/worktree.py` | KEEP | Git worktree for parallel work |
| **Config** | `swarm/config.py` | ADAPT | Project registry |
| **Ops Center UI** | `src/components/ops-center/` | ADAPT | StatusRibbon, FleetSidebar |
| **LiveFeed** | `src/components/live-feed.tsx` | ADAPT | Real-time event stream |
| **StatsBar** | `src/components/stats-bar.tsx` | KEEP | Top-level metrics display |
| **SpawnModal** | `src/components/spawn-modal.tsx` | ADAPT | Mission creation dialog |
| **Kanban Board** | `src/components/ops/KanbanBoard.tsx` | ADAPT | Task board by status |
| **Templates page** | `src/app/templates/page.tsx` | KEEP | 19 mission templates |
| **API routes** | `src/app/api/*/route.ts` | ADAPT | 26+ endpoints for tasks/teams |

### From Admin Dashboard (admin-dashboard/) — PATTERNS

| Component | File | Reuse | Notes |
|-----------|------|-------|-------|
| **Auth system** | `src/lib/auth.ts` | ADAPT | HMAC session cookies (simple) |
| **Agent Grid** | Mission control components | REFERENCE | Agent card layout patterns |
| **Escalation system** | Escalation components | REFERENCE | 3-tier alert pattern |
| **WebSocket RPC** | Gateway connection code | REFERENCE | Connection management patterns |
| **Vercel deploy tracking** | `src/lib/vercel-api.ts` | KEEP | Deploy status monitoring |

---

## 3. WHAT TO BUILD (in priority order)

### Priority 1: Collector Server (THE CRITICAL MISSING PIECE)
**File**: `collector/server.ts` (currently empty in Jarvis)

A Bun/Node HTTP + WebSocket server that:
1. Receives POST requests from Claude Code PostToolUse hooks
2. Stores events in Supabase `jarvis_hook_events` table
3. Streams events via WebSocket to connected dashboard clients
4. Tracks agent sessions (registers on SessionStart, updates on activity)
5. Calculates token costs per session

Endpoints:
- `POST /event` — Receive hook event from Claude Code
- `GET /agents` — List active agents (REST fallback)
- `GET /ws` — WebSocket connection for real-time streaming

### Priority 2: Discord Monitor
A discord.js process (or addition to the collector) that:
1. Connects using a third bot token (or one of the existing ones)
2. Listens to messageCreate on #claude-chat and #CC-chat
3. Stores messages in Supabase `discord_messages` table
4. Streams messages to dashboard via WebSocket
5. Provides `POST /discord/send` endpoint for dashboard to send messages

### Priority 3: Hook Configuration
Configure both Claude Code instances to POST to the collector:
```json
{
  "hooks": {
    "PostToolUse": [{
      "hooks": [{
        "type": "http",
        "url": "http://localhost:7777/event",
        "headers": { "X-Agent-Id": "claude-1" }
      }]
    }],
    "SessionStart": [{
      "matcher": "startup",
      "hooks": [{
        "type": "http",
        "url": "http://localhost:7777/event",
        "headers": { "X-Agent-Id": "claude-1" }
      }]
    }]
  }
}
```

### Priority 4: Dashboard Assembly
Fork Jarvis Dashboard and add:
1. Discord message feed panel (new component)
2. Message input box per agent (new component)
3. Configure with Kruz's projects
4. Deploy to Vercel

---

## 4. SUPABASE TABLES NEEDED

Jarvis already defines most of these. Add:

```sql
-- Already exists in Jarvis schema:
-- jarvis_sessions, jarvis_hook_events

-- NEW: Discord message tracking
CREATE TABLE discord_messages (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  channel_id TEXT NOT NULL,
  channel_name TEXT,
  message_id TEXT NOT NULL,
  author_id TEXT NOT NULL,
  author_name TEXT NOT NULL,
  is_bot BOOLEAN DEFAULT false,
  content TEXT NOT NULL,
  agent_label TEXT, -- 'claude-1', 'claude-2', 'user'
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Enable Realtime on both tables
ALTER PUBLICATION supabase_realtime ADD TABLE discord_messages;
ALTER PUBLICATION supabase_realtime ADD TABLE jarvis_hook_events;
ALTER PUBLICATION supabase_realtime ADD TABLE jarvis_sessions;
```

---

## 5. PROCESS STARTUP

When Kruz opens his computer, the following should be running:

```bash
# 1. Start the collector server (receives hooks + monitors Discord)
cd ~/Desktop/Projects/jarvis-dashboard && bun run collector/server.ts

# 2. Start Claude 1 with hooks configured
claude --channels plugin:discord@claude-plugins-official

# 3. Start Claude 2 with hooks configured
DISCORD_STATE_DIR="$HOME/.claude/channels/discord2" claude --channels plugin:discord@claude-plugins-official

# 4. Dashboard is always available at jarvis.buildkit.store (Vercel)
```

Eventually: wrap steps 1-3 in a single `fleet start` command.

---

## 6. DASHBOARD LAYOUT (single page, no navigation needed)

```
┌─────────────────────────────────────────────────────────────────┐
│ CLAUDE COMMAND CENTER          [Claude 1: active] [Claude 2: active] │
├───────────────────────┬─────────────────────────────────────────┤
│                       │                                         │
│   CLAUDE 1            │   CLAUDE 2                              │
│   ┌─────────────────┐ │   ┌─────────────────┐                  │
│   │ AgentSwimLane   │ │   │ AgentSwimLane   │                  │
│   │ (tool timeline) │ │   │ (tool timeline) │                  │
│   │                 │ │   │                 │                  │
│   │ Edit auth.py    │ │   │ Bash: npm test  │                  │
│   │ Read config.ts  │ │   │ Grep: "error"   │                  │
│   │ Bash: git push  │ │   │ Edit: page.tsx  │                  │
│   └─────────────────┘ │   └─────────────────┘                  │
│                       │                                         │
│   Discord Messages    │   Discord Messages                      │
│   ┌─────────────────┐ │   ┌─────────────────┐                  │
│   │ #claude-chat    │ │   │ #CC-chat        │                  │
│   │ feed            │ │   │ feed            │                  │
│   └─────────────────┘ │   └─────────────────┘                  │
│                       │                                         │
│   [Type to direct...] │   [Type to direct...]                   │
│                       │                                         │
├───────────────────────┴─────────────────────────────────────────┤
│ UNIFIED EVENT FEED          │  COST TODAY: $0.42                │
│ ┌───────────────────────────┤  Claude 1: $0.28 ████████░░      │
│ │ C1: Edit auth.py     3:42│  Claude 2: $0.14 ████░░░░░░      │
│ │ C2: Bash npm test    3:41│                                    │
│ │ C1: Read config.ts   3:40│  SESSION HISTORY                   │
│ │ C2: Grep "error"     3:39│  ┌────────────────────────────┐   │
│ └───────────────────────────┤  │ SessionBrowser             │   │
│                             │  └────────────────────────────┘   │
└─────────────────────────────┴───────────────────────────────────┘
```

---

## 7. IMMEDIATE BUILD ORDER

1. **Create third Discord bot** (NexusMonitor) — 5 min in Discord Developer Portal
2. **Build collector server** (`collector/server.ts`) — HTTP + WebSocket + discord.js monitor, ~300 lines
3. **Add Discord monitoring** to collector — reads ALL Claude channels via third bot
4. **Add Discord sending** to collector — POST endpoint for dashboard to send messages to any channel
5. **Add DiscordFeed + MessageInput components** to Jarvis dashboard — ~150 lines
6. **Create Supabase tables** — `discord_messages` + ensure `jarvis_hook_events` exists
7. **Configure PROJECT_PRESETS** — add Kruz's projects
8. **Deploy dashboard** to Vercel, start collector locally
9. **Test end-to-end** — both Claudes working, hooks fire, collector captures, dashboard shows real-time

NOTE: Hooks are ALREADY configured (`jarvis-event.sh` in `~/.claude/settings.json`). No hook config changes needed.

---

## 8. SCALING TO 4-10+ CLAUDE INSTANCES (Discord-First Vision)

The system scales naturally because Discord IS the orchestration layer:

```
Discord Server ("The Terminal")
├── #ops (Monitor bot — unified status view)
├── #frontend-claude    → Claude 3 (frontend specialist, own bot token)
├── #backend-claude     → Claude 4 (Python/Django, own bot token)
├── #devops-claude      → Claude 5 (deploys/infra, own bot token)
├── #research-claude    → Claude 6 (research/audits, own bot token)
├── #pl-engine-claude   → Claude 7 (work projects, own bot token)
├── #creative-claude    → Claude 8 (content/marketing, own bot token)
├── ...etc
```

Each Claude instance:
- Has its own Discord bot token (free, 5 min to create)
- Runs with `DISCORD_STATE_DIR=~/.claude/channels/discord{N}`
- Listens to its own channel
- Has its own persona via `--agent` flag or CLAUDE.md
- All share the same hooks config → all events go to collector

The Monitor bot:
- Watches ALL channels
- Posts summary embeds to #ops every N minutes
- Responds to slash commands: `/status`, `/assign`, `/spawn`, `/kill`
- Alerts on stuck/errored instances
- All controllable from phone via Discord app

Startup: `fleet start` script launches all N instances + collector

To add a new Claude: create bot token → add channel → add to fleet config → `fleet spawn research-2`
