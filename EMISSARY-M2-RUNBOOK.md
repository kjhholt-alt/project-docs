# EMISSARY M2 DEPLOYMENT RUNBOOK — Spaceship → MS-A2

*Written 2026-07-05 by a cloud lane, grounded against the live system on disk (bridge.py deploy, watchdog task, identity kit, fleet-node plan). Companions: [EMISSARY-IDENTITY-KIT.md](EMISSARY-IDENTITY-KIT.md), [FLEET-NODE-PLAN.md](FLEET-NODE-PLAN.md), [ASSISTANT-V1-PLAN.md](ASSISTANT-V1-PLAN.md).*

**Scope:** move the Emissary resident (chatbridge) from the gaming PC (`Spaceship`) to the MS-A2 node. M2 gate (identity kit §6): *main PC off 24h → brief still lands; forced kill → Emissary first to report its own restart with gap size; 7 days zero silent gaps.*
**Baseline assumed done:** FLEET-NODE-PLAN.md steps 1–7.

**Ground truth as of writing (verified on disk 2026-07-04):**
- Live bridge: `~/.operator/chatbridge/bridge.py` — REST poll every 5s on #claude-chat + #decision-queue.
- Secrets from `operator-core/.env`: DISCORD_BOT_TOKEN, OPERATOR_OWNER_DISCORD_ID, CLAUDE_CODE_OAUTH_TOKEN.
- Resident state: session 2 turns old; **no rotation has ever fired; `memory/` does not exist yet.** SOUL/BOOT/HEARTBEAT are still drafts in the kit, not deployed files.
- Watchdog: schtask `\ChatBridgeWatchdog`, 10-min repeat, VBS→PS1 with a **pinned pythonw path that will differ on the node**.
- Hardcoded in bridge.py: WORKDIR, ENV_FILE, GL_SCRIPT, NOTES_SCRIPT — all absolute Spaceship paths.

**Three key findings:**
1. **`memory/` doesn't exist yet** — the kit-vs-code memory-layout mismatch (ASSISTANT-MEMORY.md vs ROLLING.md; flat dailies vs daily/) costs ZERO to fix now, before any memory exists (pre-move item 1.2).
2. **Hard recommendation: fresh session seeded from flushed memory, never copy resident_state.json** — session ids are machine-local dangling pointers; the kit's own doctrine says memory IS the identity.
3. **Hard recommendation: fail-closed `owner_host` guard in BOTH bridge and watchdog on BOTH boxes** — kills the double-bridge double-reply failure mode structurally; tested adversarially in step 2.8.

---

## 1. PRE-MOVE BUILD (on Spaceship, before the box arrives)

Each item verifiable locally, zero node dependency. 1.5 (config extraction) unblocks everything after it.

- **1.1 Deploy the identity files** — extract SOUL/BOOT/HEARTBEAT from the kit into real files next to the bridge; host-neutralize SOUL's residence line ("I run on whichever box owns the channel; ownership is in OWNER"). *Verify: 3 files exist; no date-bound claims.*
- **1.2 Restructure memory tiers to kit convention** — `write_resident_memory()` → `memory/daily/<date>.md`; rolling target → `memory/ROLLING.md`; seed ROLLING with the kit §5 skeleton (open threads / watch items / promises / anti-nag ledger / posture flags / last-flushed stamp). *Verify: test flush lands in both files; delete test block.*
- **1.3 Add `--flush` CLI flag** — on-demand rotation flow for move day. *Verify: `py bridge.py --flush` → flush block written, fresh session_id, turns 0, rotating false.*
- **1.4 Silent-heartbeat writer** — every 30 min write `heartbeat.json` {ts, iso, host, pid, turns_total}, atomic (temp + os.replace), posts NOTHING. *Verify: 60s test interval → mtime advances, zero channel messages.*
- **1.5 Host config + ownership guard (the anti-double-bridge rail)** — new `bridge_config.json` {owner_host, workdir, env_file, gl_script, notes_script, quiet_hours, brief_window}. Bridge re-checks owner_host vs hostname at startup + once/minute; mismatch → log NOT OWNER, exit(1). Watchdog reads pythonw path from a variable AND exits 0 without spawning if owner_host ≠ COMPUTERNAME (second layer). *Verify: 3 checks — normal run works; wrong owner exits ≤5s; watchdog spawns nothing on wrong owner.*
- **1.6 Boot sequence in the bridge** — `boot_sequence()` at loop start + after rotation: assemble boot prompt (SOUL → ROLLING → dailies → greenlight status w/ 30s timeout), compute gap from heartbeat.json (same-host only; missing → "gap unknown", never fabricate), posture check FIRST (game/quiet → no post, pending_boot_report flag → first allowed message leads with it), else exactly one message in BOOT.md's fixed shape, gap always line 1. *Verify: 4 checks (gap math, missing heartbeat, silent posture boot, --dry normal).*
- **1.7 Morning-brief hook** — `--brief`: ONE message (queue deltas, overnight anomalies from dailies, aging gate items, 24h heartbeat-gap summary). A layer ON GreenlightMorningBrief, not a replacement. *Verify: --brief --dry prints real numbers; live run posts exactly one message.*
- **1.8 Snapshot rollback materials** — export ChatBridgeWatchdog XML; commit the full chatbridge code set (NOT state/log/heartbeat/memory) to the repo — the node deploys from git. *Verify: XML has the 10-min trigger; git clean; HEAD lists every needed file.*

## 2. MOVE DAY (order is load-bearing — old poller dead and unrevivable BEFORE the new one polls)

- **2.1 Preflight node:** `ssh kruz-svc@ms-a2 "hostname && py --version && claude -p 'say NODE-OK'"` — all three or stop.
- **2.2 NEW Discord bot token — never reuse.** Create a fresh application/bot ("Emissary"), same channel perms. Do NOT reset the old token mid-move (kills the running bridge). New token → node's operator-core/.env via resolver pattern. *Verify: REST GET on #claude-chat with new token → 200; secrets.py where resolves.*
- **2.3 Final flush + stop on Spaceship:** (1) post "emissary migrating to ms-a2 — brief outage" (the gap anchor); (2) `py bridge.py --flush`; (3) `schtasks /delete /tn ChatBridgeWatchdog /f` — DELETE, not disable; (4) kill the bridge process; (5) set Spaceship owner_host = "ms-a2" (guard now blocks resurrection). *Verify: flush visible; task not found; process list empty; wait 12 min → still empty. All four before proceeding.*
- **2.4 State migration — fresh session, memory-seeded, nothing else moves.** Moves: memory/ (with final flush), SOUL/BOOT/HEARTBEAT/instructions/nomcp/bridge_config (owner_host → "ms-a2"), code via git pull. NEVER moves: resident_state.json, state.json, logs, heartbeat, attachments. *Verify: final-flush block readable on node; resident_state.json absent; git hash matches 1.8.*
- **2.5 Node-local path fixes** — bridge_config paths + watchdog pythonw variable. *Verify: `py bridge.py --dry "what's in the greenlight queue?"` → real numbers (proves env, token, paths, instructions, memory seed all resolve).*
- **2.6 Initialize watermark + first live boot:** `--reset` (never replay migration backlog), then foreground `py bridge.py`. *Verify: exactly ONE message, line 1 = honest migration gap; phone test → ack + reply from the Emissary bot identity; Ctrl-C.*
- **2.7 Register watchdog + brief on the node** (service account self-registers, no admin). *Verify: /run → bridge within 30s; kill it → respawn ≤10 min AND Emissary posts its restart report (free rehearsal of test 3.2).*
- **2.8 DNS-of-truth adversarial check:** attempt resurrection on Spaceship — bridge must exit NOT OWNER, watchdog must spawn nothing; meanwhile a phone message gets exactly ONE reply. *Single-reply under attempted-dual-start = the incident is structurally dead.*

## 3. CUTOVER TEST PROTOCOL — M2 acceptance (~9 days, results logged in memory/daily/)

| # | Test | PASS criteria |
|---|---|---|
| 3.1 | Echo + single-owner | 1 ack, 1 reply, Emissary identity; zero bridge.py on Spaceship |
| 3.2 | Forced-kill self-report | First message after kill is Emissary's own restart report ≤11 min, gap ±2 min, unprompted |
| 3.3 | Gap-size honesty (2h engineered hole) | Reported gap 2h ±5 min; no backfill, no "still fine" reconstruction |
| 3.4 | Quiet-hours boot | Zero posts during game posture; held report posts honestly after clearing |
| 3.5 | 24h main-PC-off | Brief lands in-window from the node; heartbeat ≤35 min gaps all day; Spaceship OFF |
| 3.6 | 7-day zero-silent-gap watch | Silent-gap count = 0; every >35 min gap has a matching self-report. Honest gaps don't fail; UNREPORTED ones fail and reset the clock |

**M2 = PASS** → revoke the old Spaceship token, mark the kit §6 M2 done.

## 4. ROLLBACK — Spaceship in <10 min

1. Kill node ownership (delete tasks, kill bridge, owner_host → "SPACESHIP"; if node unreachable: revoke the Emissary token — a poller with a dead token is deaf and mute).
2. Rescue memory one-way node→Spaceship (skip only if bricked — then say so in-channel; honesty applies to operators too).
3. Reclaim ownership in Spaceship config.
4. Re-register watchdog from the 1.8 XML snapshot.
5. `--reset` + watchdog boot → honest gap report from the OLD bot identity, fresh session seeded from rescued memory.
6. Node token stays revoked before any retry.

## 5. RISK TABLE

| Risk | Mitigation | Residual |
|---|---|---|
| Dual-poll double-replies | owner_host fail-closed in bridge AND watchdog, both boxes; DELETE not disable; adversarial test 2.8 | Hand-editing both configs wrong — rerun 2.8 after any config touch |
| Token confusion | Separate applications; tokens only in each box's own .env; old token = the rollback path until M2 passes | Two valid tokens during the window — ownership rail controls polling, not tokens |
| Clock drift poisoning gap math | Same-host timestamps only; w32tm check in preflight; boot message carries ISO timestamp (wrong wall clock visible in-channel) | BIOS-level jumps |
| Windows Update reboot vs session | Auto-logon + logon watchdog → back ≤10 min; crash-safe `rotating` flag forces clean fresh session, never half-flushed resume; reboot = honest gap report (correct behavior) | An in-flight unflushed turn — bounded loss by design |
| Memory-file sync | **Single writer + one-way backup, no live sync.** Node memory/ = sole authority, local git repo, auto-commit at each flush; nightly one-way copy to Spaceship memory-backup/ (NOT the live path — promotion is a conscious rollback step) | ≤24h memory loss if node disk dies between backups; dailies recoverable in spirit from the channel |
| Shared Max rate limit | Resident runs haiku; a limited turn errors in-channel, not silence — detectable, honest | Watch week-1 hits |
| Watchdog process visibility | Bridge + watchdog same service account | Manual bridges as another user — don't, except foreground smoke tests |
