# Assistant v1 — "have we built it?" Synthesis & Plan

**Date:** 2026-07-04 · **Verdict: YES, ~70% built — and the research says DON'T adopt OpenClaw. Build-on-own, borrow named pieces, ship v1 the week the MS-A2 arrives.**

Two independent lanes converged tonight:
- [ASSISTANT-STACK-INVENTORY.md](ASSISTANT-STACK-INVENTORY.md) — audited our stack: **65-70% of an always-on PA already runs today** (bridge, greenlight, hermes, Atlas, 117 scheduled flows, murmur). Missing: the assistant *layer* — persistent identity, judgment-based proactivity, one addressable brain.
- [ASSISTANT-HARNESS-RESEARCH.md](ASSISTANT-HARNESS-RESEARCH.md) — surveyed the market: OpenClaw (382k stars) would add channel reach + polish but costs a second runtime, a subscription/API fork (Anthropic banned third-party OAuth 2026-04), and the year's worst security record (42k exposed instances, CVE-2026-25253, ~20% malicious marketplace). **We own ~80% of its value at ~20% of its attack surface — and our greenlight gate is ENFORCED, theirs is advisory.**

## The v1 shape (name TBD — Kruz's call; the retired ClawBot's "Prime" SOUL.md/BOOT.md identity-contract pattern is worth reviving as a *pattern*, not code)

**MS-A2 = the assistant box.** Our stack, promoted to a resident: one supervised daemon identity wrapping bridge + greenlight + hermes + Atlas, Tailscale-only, new bot tokens, non-admin service account.

**Build order (from the research, cross-checked against the inventory gaps):**
1. Base hardening (day 1): Tailscale + ACLs, zero inbound ports, sleep off, non-admin account.
2. Port the runtime (days 1-2): runtime clone + bridge + greenlight + flows; verify headless claude auth.
3. Watchdog consolidation (day 2): one supervisor + heartbeat-to-Discord + silence alerts (inventory gap #5).
4. **The assistant loop (days 3-5) — the real build**: persistent conversational session on the bridge (inventory gap #1, highest leverage: today every Discord message spawns a fresh `claude -p` with 8-message fake context). Borrow OpenClaw conventions: memory-flush-before-compaction, daily working notes, pairing codes. Wire bridge → Atlas recall + greenlight state + hermes status (gap #4: one brain).
5. Phone reach (week 2): Telegram adapter on OUR bridge (official API — never Baileys/WhatsApp). Voice: murmur piped into the bridge.
6. Later, only if needed: NanoClaw (MIT, ~15 files, Claude Agent SDK native) as a channel-adapter parts bin — never as a second brain.

**Hard rails carried over:** greenlight gates stay unskippable; lethal-trifecta legs capped per session (untrusted content ⊕ secrets ⊕ exfil tools — never all three); no marketplace skills; pin versions; audit log everything; alert on silence, never fake freshness.

**Fix flagged en route:** Orbit's "HEARTBEAT DOWN" can't distinguish "hermes queue empty" from "hermes dead" (tonight's false alarm) — a stale-trust bug in the cockpit's own terms; queue as a small item.

**Status:** research complete, plan ready. Awaiting Kruz's !ok on the v1 build item (greenlight) + the MS-A2 arrival. Everything in step 4 can be built and tested on the gaming PC *before* the box arrives, then ported in step 2's rsync.
