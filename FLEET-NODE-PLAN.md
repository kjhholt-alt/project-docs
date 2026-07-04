# FLEET-NODE-PLAN.md

**Date:** 2026-07-04 · **Subject:** MINISFORUM MS-A2 as a dedicated agent-fleet node · **Verdict up front: BUY — but the $439 unit is not the CPU you think it is. Buy the 16-core variant (~$479–559). Recommended build: Tier B, ~$1,825 all-in.**

*Produced by a cloud research lane during Kruz's Deadlock session (covers gl-0047 + gl-0057). Prices verified live 2026-07-04.*

---

## 0. Premise correction (read this first)

The "$439 MS-A2 with Ryzen 9 9955HX" does not exist. What's real, as of today:

| Variant | CPU | Cores | Barebone price | Notes |
|---|---|---|---|---|
| MS-A2-7745 | Ryzen 7 7745HX (Zen 4) | 8C/16T | **$439** (20% off $549) | This is the $439 unit ([Minisforum store](https://store.minisforum.com/products/minisforum-ms-a2-workstation), [Notebookcheck](https://www.notebookcheck.net/Minisforum-relaunches-mini-PC-with-even-cheaper-new-versions.1255551.0.html)) |
| MS-A2-8945 | Ryzen 9 8945HX (rebadged 7945HX) | 16C/32T | $599 list; **~$479 if the same 20% promo applies — verify at checkout** | Best value if the discount is live |
| MS-A2-7945 | Ryzen 9 7945HX (Zen 4) | 16C/32T | **$559** | The known-good 16-core price |
| MS-A2-9955 | Ryzen 9 9955HX (Zen 5) | 16C/32T | **$799** ([Amazon](https://www.amazon.com/MINISFORUM-AMD-Ryzen-9955HX-Barebone/dp/B0F8JG2SHN), [VideoCardz](https://videocardz.com/newz/minisforum-launches-ms-a2-mini-workstation-with-ryzen-9-9955hx-price-starts-at-799)) | Only ~3% faster than 7945HX ([nanoreview](https://nanoreview.net/en/cpu-compare/amd-ryzen-9-9955hx-vs-amd-ryzen-9-7945hx)) |

The store page headlines every variant as "Ryzen 9 9955HX," which is how the $439/9955HX conflation happens. The 7745HX is a genuinely good chip, but it's **half the all-core throughput** of the 16-core parts (Cinebench R23 MC ~18.6k vs ~28–30k) — and all-core throughput (cargo builds, parallel test suites) is exactly what a fleet node is for. For +$120–160 you double the build engine. **Action at checkout: select the cheapest 16-core variant showing (8945HX or 7945HX).** If by some miracle the dropdown really shows 9955HX at $439 — instant buy, skip the rest of this section.

**What the barebone includes (verified):** chassis, soldered CPU + motherboard, integrated cooling (3 heat-pipe copper + turbo fan), 240W external power brick (19V/12.63A), Radeon 610M iGPU, WiFi 6E/BT, 2× 2.5GbE RJ45 + 2× 10G SFP+, 3× M.2 (2280/22110/U.2), PCIe x16 (x8 electrical) slot. **No RAM, no SSD, no OS.** Nothing else to buy for cooling or power. ([Minisforum](https://store.minisforum.com/products/minisforum-ms-a2-workstation), [ServeTheHome review](https://www.servethehome.com/minisforum-ms-a2-review-an-almost-perfect-amd-ryzen-intel-10gbe-homelab-system/))

---

## 1. Total real cost to operational — three tiers

2026 shortage pricing is real and verified. Current DDR5-5600 SODIMM street prices (all in stock today):

| Kit | Price | $/GB | Source |
|---|---|---|---|
| 32GB (2×16) | ~$465 | $14.5 | [Corsair Vengeance SODIMM](https://www.corsair.com/us/en/p/memory/cmsx32gx5m2a5600c48/vengeance-ddr5-sodimm-32gb-2x16gb-ddr5-5600mts-pc5-44800-cl48-1-1v-cmsx32gx5m2a5600c48) — skip, worst $/GB |
| 48GB (2×24) | ~$599 | $12.5 | [Crucial CT2K24G56C46S5](https://www.crucial.com/memory/ddr5/ct2k24g56c46s5) |
| 64GB (2×32) | ~$835 | $13.0 | [Newegg, Crucial CT2K32G56C46S5](https://www.newegg.com/crucial-64gb-ddr5-5600-cas-latency-cl46-laptop-memory/p/N82E16820156317) — $834.98 |
| 96GB (2×48) | ~$1,297 | $13.5 | [Newegg, Crucial CT2K48G56C46S5](https://www.newegg.com/crucial-ddr5-classic-96gb-ddr5-5600-cas-latency-cl46-laptop-memory/p/N82E16820156362) — $1,296.95 |

(Market average ~$14.12/GB per [rampricesusa.com](https://rampricesusa.com/); pre-shortage this 64GB kit was ~$131. You'll see fantasy prices in stale trackers — a "$268 96GB kit" and a "$99 A-Tech kit" both appear in aggregators; the live retailer pages above are reality. RAM prices spiked 3–4× and analysts see no relief before late 2027 — [wccftech](https://wccftech.com/memory-ddr5-ddr4-shortages-last-till-q4-2027-higher-prices-throughout-2026/), [TechTimes/Gartner +130% forecast](https://www.techtimes.com/articles/317872/20260605/ram-prices-2026-buy-now-wait-gartner-forecasts-130-memory-cost-surge.htm).)

**Storage** (NAND shortage also hit): 2TB Gen4 NVMe — Crucial P310 2TB ~$300 (cheapest mainstream), WD SN850X 2TB ~$379, 990 Pro ~$400 ([dropreference SSD crisis tracker](https://dropreference.com/en/blog/news/ssd-price-increase-2026-nand-flash-crisis), [Tom's SSD index](https://www.tomshardware.com/pc-components/ssds/ssd-price-tracking-2026-lowest-price-on-every-m-2-ssd)). 1TB ≈ $160–190 (estimate from 2TB pricing). The P310-class drive is plenty for a build node.

**OS:** Windows 11 Pro — $199 retail (Microsoft), ~$120 legit OEM reseller on sale, ~$25 gray-market keys exist but get revoked ([Tom's Hardware guide](https://www.tomshardware.com/software/windows/windows-11-free-or-cheap)). Planning number: **$130**. ($0 option: Windows runs unactivated indefinitely with a watermark and no personalization — workable for a headless node, but license it if it becomes production.)

**Misc:** HDMI dummy plug ~$8 (headless display for screenshot loops).

### The tiers

| | **Tier A — Toe-in** | **Tier B — Right-size (RECOMMENDED)** | **Tier C — Max node** |
|---|---|---|---|
| Barebone | 7745HX (8C) — $439 | **7945HX/8945HX (16C) — $559** (or ~$479 if promo applies) | 9955HX (16C Zen 5) — $799 |
| RAM | 48GB — $599 | **64GB — $835** | 96GB — $1,297 |
| NVMe | 1TB — ~$175 | **2TB — $300** | 2TB — $300 |
| Win 11 Pro | $130 | **$130** | $130 |
| Dummy plug | $8 | **$8** | $8 |
| **Total** | **~$1,351** | **~$1,832** (~$1,752 w/ 8945 promo) | **~$2,534** |

Why Tier B: the 16-core doubles build throughput for +$120; 64GB is the sweet spot because the measured fleet footprint is small (~145MB/session; 39 sessions + 8 workers = 5.5GB RSS — Headroom telemetry 2026-07-02) but cargo/rustc lanes spike 10–20GB, and with RAM forecast to keep climbing, buying the bigger kit now is the hedge — "upgrade later" costs more later. 96GB is overkill for a node that will realistically run 2 build lanes + 20 sessions; the extra $462 doesn't buy shipped units. The 9955HX's +3% doesn't buy $240.

**Sticker shock, acknowledged:** the RAM costs more than the computer. That's 2026. It's still the cheapest way to buy 16 Zen 4 cores + 64GB of *isolated* capacity.

---

## 2. OS + stack: Windows 11 Pro first, Linux later (maybe never)

**Recommendation: Windows 11 Pro.** Not close, for the first posture:

- The entire fleet runtime is Windows-native: `schtasks` scheduled flows (43 of them), PowerShell launchers, `pythonw`/VBS no-window wrappers, Windows path assumptions everywhere, the headless-spawn doctrine tooling, `greenlight.py` dispatch, the runtime-clone self-heal pattern. Porting any of it to Linux is a project; mirroring it is a checklist.
- **Orbit is the hard constraint:** Rust + GPUI builds target Windows (GPUI's Windows renderer is DirectX 11 — [Zed's Windows report](https://zed.dev/blog/windows-progress-report)); Windows build verification and screenshots must happen on a Windows box. The node's Radeon 610M is a real GPU, so GPUI runs natively there — no software-rasterizer fallback needed. (Open question flagged: GPUI has no documented offscreen/headless mode; over RDP it can land on the Basic Render Driver and need `ZED_ALLOW_EMULATED_GPU=1` — [zed#42632](https://github.com/zed-industries/zed/issues/42632). The dummy HDMI plug + auto-logon console session sidesteps this: real iGPU, real display, screenshots work. **Verify in migration step 11.**)
- Pro (not Home) because you want **RDP host** for the rare hands-on session, plus group policy for auto-logon/lock behavior.

**What Ubuntu Server would unlock later:** ~2–3GB less OS overhead, no Windows Update reboot roulette, cleaner daemon management (systemd vs schtasks), Docker without WSL2's RAM tax, and honestly nicer headless ergonomics. The pragmatic path if you ever want it: keep the node Windows, and run **WSL2 with a hard memory cap** for any Linux-only workload. Full Linux conversion only makes sense if the fleet's Windows-isms get abstracted first — that's a rewrite you haven't scheduled and don't need.

**Stack checklist for the node:** Git + gh CLI, Python 3.14 via `py` launcher (mirror main PC), Node LTS, Rust + VS Build Tools 2022 (MSVC), Claude Code CLI, Tailscale, OpenSSH Server, psutil. Mirror the `~/.operator/` runtime-clone layout so launchers work unmodified.

---

## 3. Work placement

Guiding fact from memory: **game mode = ZERO fleet lanes, proven 3× — disk/AV/scheduler churn ignores priority and affinity**. So the split isn't "heavy vs light," it's "does it need Kruz's desktop/GPU or not."

| Workload | Placement | Why |
|---|---|---|
| Headless `claude -p` workers (greenlight dispatch) | **NODE** | Zero display needs; the core win. API-bound, cheap locally |
| Cargo/rustc builds (Windows targets, incl. Orbit CI builds) | **NODE** | The single worst gaming-PC offender (RAM+disk churn). 16C Zen 4 eats these |
| Python test suites (pl-engine 3,500+, buildforge ~490, etc.) | **NODE** | Pure CPU, no display |
| Calibration/benchmark model-call runs (llm-benchmark-suite, TPVS) | **NODE** | Near-zero local compute, API-bound — perfect filler load |
| Scheduled cron flows (~43): clipforge produce, radars, briefs, index rebuilds | **NODE** (migrate in waves) | Most are data-in/data-out Python. Move the 08:00/08:20 clipforge + nightly index first |
| Discord bridge + Greenlight pump | **NODE, phase 2** | It's the always-on front door — belongs on the always-on box (survives gaming-PC reboots/sleep; remember the golf-burn sleep kill). Keep on main PC until node is proven, then move |
| GPUI screenshot-verification loops (Orbit visual checks) | **NODE with dummy plug — verify first**; fallback main PC | Radeon 610M + dummy HDMI + auto-logon should work; confirm in step 11 |
| Game mods/overlays, anything touching HIS games, TrueSens, Headroom overlay | **GAMING PC** | Needs the real game, real display, real 3090 |
| Interactive Orbit use, desktop Claude sessions, anything Kruz drives | **GAMING PC** | It's his cockpit |
| Anything needing the 3090 (rare — it's 96% idle for fleet work) | **GAMING PC** | Node has no dGPU (PCIe x16 slot exists if that ever changes) |

Net effect: the gaming PC becomes the cockpit + game box; the node becomes the factory floor. Headroom's `game_mode` flag stops meaning "fleet dies" and starts meaning "fleet lives elsewhere."

---

## 4. Networking + access

**Recommendation: Tailscale + OpenSSH for plumbing, Greenlight as the brain (node-aware dispatcher), git/GitHub as the only file-sync mechanism.**

- **Transport: Tailscale** on both machines (free tier, WireGuard, zero port-forwarding, works when you're away). Node also gets a static LAN IP on 2.5GbE for fast local git/SMB. Skip WinRM (auth ceremony, worse ergonomics than SSH). Skip an SMB job-folder as the primary queue (file-lock races, silent-failure mode — you already learned webhooks/queues need GET-check verification).
- **Dispatch: extend Greenlight, don't invent a new queue.** `greenlight.py` already does propose → approve → detached headless dispatch → result post. Add a `node` field to queue items and run a **pull-model pump on the node**: a `GreenlightNodePump` scheduled task that claims items tagged `node:ms-a2` (or untagged CPU-heavy classes) and runs them locally. Pull beats push — the node keeps working when the main PC is gaming, rebooting, or asleep. v1 can be even simpler: main PC's existing pump runs `ssh ms-a2 -- <headless spawn>` for node-tagged items; graduate to the node-local pump once queue state has a shared home (read-only SMB export of `~/.operator/greenlight/` with atomic claim-files, or a git-backed queue — decide during build-out, both are one-evening changes).
- **Auth: mint a separate `CLAUDE_CODE_OAUTH_TOKEN` on the node** (`claude setup-token`, paste into the node's `operator-core/.env` — remember it prints, it doesn't store). Same Max account, so rate limits are shared, but separate tokens mean independent rotation and unambiguous 401 debugging — the go-live outage taught this. Copy the greenlight env-scrub pattern verbatim.
- **Git: node is a full first-class clone**, not a file share. `gh auth login` with a fine-grained PAT (or a machine SSH deploy key), clones from GitHub, pushes branches, PRs gate in Discord exactly as today. Never share working trees over SMB between machines — that's the shared-checkout collision bug with a network in the middle.
- **Discord stays the pane of glass:** node results post to the same channels via the same webhook/bot patterns; `!fleet` grows a node column eventually.

---

## 5. Migration checklist (unbox → first lane)

1. **Order:** 16-core barebone (~$479–559), 64GB Crucial DDR5-5600 SODIMM kit, 2TB NVMe, dummy HDMI plug, Win 11 Pro key. *Verify: checkout screenshot shows a 16C CPU (8945HX/7945HX/9955HX), not 7745HX.*
2. **Assemble:** SODIMMs seated, NVMe in slot 1. *Verify: BIOS shows 64GB @ 5600 and the SSD; while there, set "restore AC power state = ON" (survive outages) and check fan curve.*
3. **Install Windows 11 Pro**, hostname `ms-a2` (or `forge-1`), local account, auto-logon enabled, **sleep/hibernate OFF** (`powercfg /change standby-timeout-ac 0` — the golf-burn lesson), Windows Update active hours set. *Verify: reboot → machine returns to logged-in desktop with no interaction.*
4. **Tailscale + OpenSSH Server** installed and enabled. *Verify: from gaming PC, `ssh kruz@ms-a2 'hostname'` returns over both LAN IP and tailnet name.*
5. **Toolchain:** Git, gh, Python 3.14 (`py` launcher), Node LTS, Rust + VS Build Tools, psutil. *Verify: `py --version`, `cargo --version`, `gh auth status` all pass over SSH.*
6. **Claude Code CLI** + `claude setup-token` → node's `operator-core/.env`. *Verify: `claude -p "say NODE-OK"` headless run returns NODE-OK with desktop app closed.*
7. **Clone the runtime:** operator-scripts + the runtime-clone layout under `~/.operator/`. *Verify: one existing launcher script runs unmodified and writes its artifact.*
8. **Smoke a real test suite:** clone pl-engine, run the full suite. *Verify: pass count matches the gaming PC's last known-good; note wall-clock time (first cross-machine benchmark).*
9. **Smoke a real build:** clone orbit, `cargo build --release` (full -j, it's allowed here). *Verify: `orbit.exe` produced; note time vs gaming PC.*
10. **First scheduled flow moved:** pick one low-stakes cron (e.g., nightly portfolio index build), register on node, **disable on main PC only after** one green run. *Verify: artifact lands + Discord ping fires from the node; `task-scheduler-watch` extended to cover node tasks (or v1: node posts heartbeat to Discord).*
11. **GPUI screenshot check:** dummy plug in, auto-logon console session, run the Orbit screenshot-verification loop. *Verify: non-black screenshot of Orbit rendering on the 610M. If RDP-only sessions break it, document "console session required" and move on.*
12. **Node-aware Greenlight:** add `node` tagging + node pump (or v1 SSH dispatch). *Verify: propose a trivial item tagged `node:ms-a2`, `!ok` it from Discord, watch it execute on the node and post DONE.*
13. **First real week:** move cargo builds + 2–3 more crons + calibration runs. *Verify: during a 6h gaming session, `!fleet`/board shows lanes completing while Headroom shows the gaming PC untouched. That's the whole point — screenshot it.*

---

## 6. Honest risks

- **The 96GB ceiling is real but irrelevant.** 9955HX-class SODIMM tops at 96GB; the measured fleet is ~5.5GB RSS for 47 processes. You will run out of Anthropic rate limit long before 64GB. If the fleet someday needs >96GB, that's a second node, not a bigger one.
- **Rate limits are the true ceiling, not hardware.** Both machines share one Max account. Nights already burn ~1.5M tokens; if night burns already brush plan limits, the node's added window partially cannibalizes rather than adds. Mitigation: the node's prime window (the 6 gaming hours) is currently near-zero usage, so it's additive until proven otherwise — watch the first week's limit hits.
- **Noise/heat/power:** measured 25W idle (up to 45W), 135–145W all-core load, ~39dB idle / ~50dB load — audible, not silent ([ServeTheHome](https://www.servethehome.com/minisforum-ms-a2-review-an-almost-perfect-amd-ryzen-intel-10gbe-homelab-system/4/)). Shelf it away from the desk. Power cost at ~50W average duty: ~$5–8/month.
- **Two-machines tax — the biggest soft cost.** Second box to patch, second token to rotate, second scheduler to watch, second disk to fill, config drift. Mitigations already in doctrine: runtime-clone self-heal, monitored_tasks paging, treat the node as cattle (its state is git + GitHub; a reimage is a checklist, not a loss). Budget one evening/month of maintenance honestly.
- **"Just buy 64GB for the gaming PC instead?" — No, and here's the math.** 64GB DDR4-3600 is ~$240–350 right now ([Patriot Viper Steel 64GB $240](https://www.amazon.com/Patriot-Viper-Steel-DDR4-3600MHz/dp/B08N688HCH)) — 5× cheaper, one machine. But telemetry killed this option: game mode = **zero** fleet lanes because disk/AV/scheduler churn causes stutter regardless of RAM and priority (proven 3×). More RAM widens the night window's session count; it recovers **none** of the ~6h/day gaming window. The units math: ~1.5M tokens/night ÷ ~150k tokens/unit ≈ **10 shipped units/night** at ~1.2 units/hour of burn. Six recovered hours/day ≈ **+7 units/day ≈ +210 units/month ≈ +70% fleet output** — plus it de-risks the night burn (no more sleep-kill, no more gaming-collision lane kills). Even at half efficiency that's ~100 units/month for a one-time ~$1.8k. The DDR4 upgrade buys ~0 of that. (Do it *later* anyway if 32GB pinches gaming itself — separate question.)
- **RAM price timing:** waiting is negative-EV — every tracked forecast (Gartner, TrendForce via wccftech) has DRAM climbing into 2027. The barebone promo will cycle; the RAM won't get cheaper.

---

## 7. Verdict

**BUY.** Specifically:

> **Tier B: MS-A2 with a 16-core CPU — 8945HX at ~$479 if the 20% promo covers it, else 7945HX at $559 — + Crucial 64GB (2×32) DDR5-5600 SODIMM (~$835) + 2TB Gen4 NVMe (~$300) + Win 11 Pro OEM (~$130) + dummy HDMI plug. Total ≈ $1,750–1,830.**

Do **not** buy the $439 7745HX thinking it's the 9955HX — at 8 cores it halves the one thing the node exists to do. Do **not** pay $799 for the 9955HX's +3%. The RAM is painful at 2026 prices and it is still worth it: this is the difference between a fleet that dies for 6 hours a day and one that ships ~70% more, and it removes the gaming PC as a single point of failure for the whole 24/7 factory.

### Sources
- [Minisforum MS-A2 store page](https://store.minisforum.com/products/minisforum-ms-a2-workstation) · [Notebookcheck: cheaper MS-A2 variants ($439 7745HX / $599 8945HX)](https://www.notebookcheck.net/Minisforum-relaunches-mini-PC-with-even-cheaper-new-versions.1255551.0.html) · [VideoCardz: 9955HX at $799](https://videocardz.com/newz/minisforum-launches-ms-a2-mini-workstation-with-ryzen-9-9955hx-price-starts-at-799) · [Amazon 9955HX barebone](https://www.amazon.com/MINISFORUM-AMD-Ryzen-9955HX-Barebone/dp/B0F8JG2SHN)
- [ServeTheHome MS-A2 review (power/noise/PSU)](https://www.servethehome.com/minisforum-ms-a2-review-an-almost-perfect-amd-ryzen-intel-10gbe-homelab-system/4/) · [Notebookcheck MS-A2 review](https://www.notebookcheck.net/Minisforum-MS-A2-review-Compact-AMD-mini-PC-with-workstation-ambitions-and-GPU-upgrade-option.1062179.0.html)
- RAM: [Newegg Crucial 64GB SODIMM $834.98](https://www.newegg.com/crucial-64gb-ddr5-5600-cas-latency-cl46-laptop-memory/p/N82E16820156317) · [Newegg Crucial 96GB SODIMM $1,296.95](https://www.newegg.com/crucial-ddr5-classic-96gb-ddr5-5600-cas-latency-cl46-laptop-memory/p/N82E16820156362) · [Crucial 48GB kit](https://www.crucial.com/memory/ddr5/ct2k24g56c46s5) · [Corsair 32GB SODIMM $464.99](https://www.corsair.com/us/en/p/memory/cmsx32gx5m2a5600c48/vengeance-ddr5-sodimm-32gb-2x16gb-ddr5-5600mts-pc5-44800-cl48-1-1v-cmsx32gx5m2a5600c48) · [rampricesusa.com ($14.12/GB avg)](https://rampricesusa.com/) · [wccftech: shortage through Q4 2027](https://wccftech.com/memory-ddr5-ddr4-shortages-last-till-q4-2027-higher-prices-throughout-2026/) · [TechTimes: Gartner +130% forecast](https://www.techtimes.com/articles/317872/20260605/ram-prices-2026-buy-now-wait-gartner-forecasts-130-memory-cost-surge.htm) · [Newegg Insider DDR5 2026](https://www.newegg.com/insider/ddr5-memory-in-2026-whats-happening-to-prices-supply-and-speed-tiers/)
- SSD: [dropreference NAND crisis tracker](https://dropreference.com/en/blog/news/ssd-price-increase-2026-nand-flash-crisis) · [Tom's SSD price index](https://www.tomshardware.com/pc-components/ssds/ssd-price-tracking-2026-lowest-price-on-every-m-2-ssd) · [GamersNexus "SSDs: WTF?"](https://gamersnexus.net/features/ssds-wtf)
- OS: [Tom's Hardware Windows 11 cheap/free guide](https://www.tomshardware.com/software/windows/windows-11-free-or-cheap)
- CPU comparisons: [nanoreview 9955HX vs 7945HX (+3%)](https://nanoreview.net/en/cpu-compare/amd-ryzen-9-9955hx-vs-amd-ryzen-9-7945hx) · [nanoreview 7945HX vs 7745HX](https://nanoreview.net/en/cpu-compare/amd-ryzen-9-7945hx-vs-amd-ryzen-7-7745hx)
- GPUI/Windows rendering: [Zed Windows progress report (DirectX 11)](https://zed.dev/blog/windows-progress-report) · [zed#42632 (emulated GPU in VMs/RDP)](https://github.com/zed-industries/zed/issues/42632)
- DDR4 comparison: [Patriot Viper Steel 64GB DDR4-3600 ~$240](https://www.amazon.com/Patriot-Viper-Steel-DDR4-3600MHz/dp/B08N688HCH)
- Fleet numbers: Headroom telemetry 2026-07-02 (39 sessions + 8 workers = 5.5GB RSS, RAM 72% of 32GB, RTX 3090 96% idle), Greenlight dispatch architecture, game-mode zero-lanes doctrine — from local fleet memory.
