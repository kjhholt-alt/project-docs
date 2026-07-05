# Assistant Stack Inventory — "have we already built it?"

**Date:** 2026-07-04 · **Question:** how close is the existing stack to an always-on personal AI assistant (for the MS-A2 box)? · **Verdict: ~65-70% built. The missing 30-35% is the assistant LAYER, not the organs — a "connect four live systems" problem, not a greenfield build.**

*Read-only audit by a fleet-analyst lane during Kruz's Deadlock session. Companion doc: the external harness landscape report (OpenClaw etc.), separate lane. See also [FLEET-NODE-PLAN.md](FLEET-NODE-PLAN.md) for the box itself.*

## Component table

| Component | Location | Maturity | Key evidence |
|---|---|---|---|
| **Discord bridge** | `~/.operator/chatbridge/bridge.py` | **LIVE, works-but-fragile** | Polls #claude-chat every 5s (REST, no gateway). Spawns a **fresh `claude -p --model haiku` per message** (bridge.py:309-325) — NOT persistent; "context" = last 8 Discord messages reconstructed each time (bridge.py:282-290). Auth = single hardcoded owner ID. Images-only attachments (5/10MB caps). Kept alive by ChatBridgeWatchdog task. |
| **Hermes a2a broker** | `hermes/` + `~/.operator/hermes/` | works-but-fragile | FSM job router, SQLite, 4 adapters. Today's "HEARTBEAT DOWN" in Orbit = **stale-queue false alarm**: heartbeat reads newest audit_log ts (last job 16:12, queue legitimately empty), while the separate sensor tick (studio/hermes_sensor.py) was alive every 30s. Orbit can't distinguish "queue empty" from "daemon dead" — trust-erosion bug worth fixing. |
| **Autonomy Director** | `~/.operator/hermes/autonomy-director/` | LIVE (advisory) | Score 100/100 "L4 full unattended" as of 17:31 today; gates what autonomy-step/loop may run, executes nothing itself. |
| **Greenlight** | `operator-scripts/tools/greenlight.py` (2,362 lines) | **LIVE** | propose→ok→dispatch; standing-approval class landed 2026-07-04 (`is_standing_eligible` line 433: qa/audit/status/docs/chore, S/M only, away-posture-gated, hard denylist on money/outreach/publish/deploy line 430); pump every 30 min. |
| **Atlas** | `~/.atlas/` (17MB db) + `atlas/` | **LIVE** | 1,045 entities, 437 edges, 1,045 bge vectors; hybrid FTS5+vector recall; atlas-mcp connected; usage logging; nightly ingest. |
| **Scheduled flows** | Task Scheduler | **LIVE — 117 non-Microsoft tasks** (~90 enabled), not "~43" | Clipforge 18, Hermes 7, Greenlight 2, Studio-OS 4, Headroom 4, OperatorScript 11, Atlas 1, + product lanes. Many post to Discord (morning briefs, evening briefs, vision desk, buildkit brief). |
| **Murmur** (voice) | `murmur/` | half-built (v0.1) | Push-to-talk Whisper → types into focused window. 37 tests; `sounddevice` dep missing for live mic; NOT wired to any assistant channel. |
| **ClawBot/OpenClaw** (retired) | `D:\Projects-Archive\clawbot-config\`, `.openclaw`, `clawchat` | **DEAD — do not revive code** | Persistent-identity Discord-DM agent "Prime": SOUL.md (personality/continuity contract), HEARTBEAT.md (check-ins), BOOT.md (wake sequence → DM "I'm online"). Built Feb 2026, retired April 2026 with the Polymarket bot it watchdogged. **The SOUL/BOOT/HEARTBEAT pattern is a reusable design reference; the code is 5 months stale and bound to a dead trading bot.** |

## Gaps, ranked by build effort (smallest first)

1. **Persistent conversational identity in the bridge** (S-M) — replace per-message `claude -p` with a long-lived session (Agent SDK) or real rolling memory injection. Highest leverage, lowest effort.
2. **Multi-channel/phone reach** (S-M) — today "phone" = Discord app; no SMS/push/voice fallback; allowlist of one hardcoded ID.
3. **Judgment-based proactive outreach** (M) — 117 cron tasks fire on schedule, not on judgment; nothing watches cross-system state and decides to speak up ("Hermes queue empty 4h — expected?").
4. **One addressable brain** (M-L) — chatbridge doesn't query Atlas, doesn't check Greenlight, doesn't know Hermes status. Wire: bridge = front door, Atlas = memory, Greenlight = hands+gates, Hermes = worker pool.
5. **Unified supervision** (M-L, mostly infra) — N independent watchdogs today; the MS-A2 move is the moment to consolidate into one supervisor + one "is everything actually up" view (Orbit is already trending there).

## Recommendation

**Don't rebuild — port and connect.** Move chatbridge + Greenlight + Hermes + Atlas to the MS-A2 as-is (real, running, instrumented), then spend the build effort on gap #1 (persistent session) and gap #4 (wiring the four into one brain). Borrow the SOUL.md/BOOT.md identity-contract *pattern* from the retired Prime, not its code.

## Unknowns flagged by the auditor

- Atlas "graph export": not in atlas/STATUS.md — it IS real (shipped 2026-07-04 for Orbit Cartograaf, deployed to ~/.atlas/bin) but undocumented in Atlas's own STATUS; doc drift to fix.
- Standing approvals are deliberately narrow — don't overstate as "full autonomy."
- Studio-OS daemon liveness inferred from sensor cadence, not process check.
