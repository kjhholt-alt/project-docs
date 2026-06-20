# Active Projects — Master Tracker
> Last updated: 2026-02-13
> Update this file after every session. Paste into Claude.ai Projects for instant context sync.
> GitHub: `kjhholt-alt` | Desktop: `C:\Users\Kruz\Desktop\Projects`

---

## All Projects Overview

| # | Project | Status | Stack | Deploy | Last Activity |
|---|---------|--------|-------|--------|---------------|
| 1 | AI Chess Coach | 🟢 Feature-complete | Next.js 14, chess.js, Lichess, Claude | Vercel (pending) | Feb 12 |
| 2 | AI Finance Brief | 🟢 MVP-complete | Next.js 14, Claude, Alpha Vantage, Resend | Vercel (pending) | Feb 11 |
| 3 | ClawBot Command Center | 🟢 Deployed | Next.js 15, Supabase, OpenClaw Gateway | Vercel (`admin.buildkit.store`) | Feb 13 |
| 4 | Outdoor CRM (AATOS) | 🟢 Deployed | React 19 + Vite, Django 5, PostgreSQL | Vercel + Railway | Feb 13 |
| 5 | CRM-v2 | 🟢 Active | Django + React | TBD | Feb 13 |
| 6 | Website Factory | 🟢 Built | Next.js 14, Prisma, Resend | Vercel (pending) | Feb 13 |
| 7 | N16 Soccer | 🟢 Deployed | Next.js 14 (static) | Vercel (`n16-soccer.vercel.app`) | Feb 13 |
| 8 | Local Website Generator | 🟡 In Progress | Next.js 15, Supabase, Puppeteer | TBD | Feb 13 |
| 9 | PC Bottleneck Analyzer | 🟢 Deployed | Next.js 16, Python scanner, Recharts | Vercel (live) | Feb 13 |
| 10 | StockApp | 🟢 Deployed | Django 5, React 18, Celery, yfinance | Railway | Feb 5 |
| 11 | Portfolio | 🟢 Built | Next.js 15, Framer Motion | Vercel (pending) | Feb 12 |
| 12 | Agent Mission Control | 🟢 Functional | FastAPI, Next.js 14, watchdog | Local only | Feb 12 |
| 13 | Creative Assets Agent | 🟡 Blocked | Node.js, Claude, Replicate, Puppeteer | CLI tool | Feb 12 |
| 14 | Trade Journal | 🟡 Sidelined | Next.js 14 + Python FastAPI, Claude | Not deployed | Feb 11 |
| 15 | Gumroad Factory | 🟢 Ready | TypeScript scripts, ts-node | Gumroad (manual) | Feb 12 |
| 16 | DayToDay | 🟢 Functional | Django 4.2, React 19, MUI 7, MSAL | Local (work) | Feb 11 |
| 17 | GameDev / poe-assistant | 🟡 Alpha | Python, Anthropic SDK, Click, Rich | CLI tool | Feb 12 |
| 18 | project-docs | 📄 Reference | Markdown, PDFs | N/A | Feb 13 |
| 19 | kitbuildshop-brand | 📄 Assets | Node.js, Puppeteer | N/A | Feb 13 |
| 20 | PathOfBuilding-repo | 📦 Archive | Lua (upstream clone) | N/A | — |
| 21 | LearingChess | 📦 Archive | Django + React (no git) | N/A | — |
| 22 | spotify-visualizer | 📦 Abandoned | Empty project | N/A | — |
| 23 | TrioMoeny | 📦 Empty | Nothing | N/A | — |
| 24 | WWI Documentary (Verdun) | 📄 Scaffold | Python, Claude API, Whisper, TTS | YouTube | Jun 20 |

---

## 🟢 AI Chess Coach

**Goal:** AI-powered chess improvement app with playable game, analysis, puzzles, coaching
**Revenue:** $7/mo subscription (free tier + Pro) — launching as free beta
**Repo:** github.com/kjhholt-alt/ai-chess-coach
**Local:** `C:\Users\Kruz\Desktop\Projects\idea3`
**Stack:** Next.js 14, TypeScript, Tailwind, shadcn/ui, chess.js, react-chessboard, Lichess API, Claude API

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Project setup & foundation | ✅ Done | 2026-02-10 |
| 2 | Basic structure (pages, API routes, components) | ✅ Done | 2026-02-10 |
| 3 | Playable chess game (AI opponent, minimax, save/resume) | ✅ Done | 2026-02-11 |
| 4 | Analysis engine + Claude AI coaching API | ✅ Done | 2026-02-11 |
| 5 | Puzzle trainer (3 modes, 60 puzzles, Elo tracking) | ✅ Done | 2026-02-11 |
| 6 | Dashboard, progress tracking, 28 achievements | ✅ Done | 2026-02-11 |
| 7 | Comprehensive testing (131 tests, 15 files, 100% pass) | ✅ Done | 2026-02-12 |
| 8 | Lichess game import + game history viewer | ✅ Done | 2026-02-11 |
| 9 | Opening explorer + repertoire builder + quiz | ✅ Done | 2026-02-11 |
| 10 | Deploy to Vercel | ⬜ Not started | |

**Pages:** 16 (1 landing + 10 dashboard + 5 auth/policy)
**API Routes:** 6 (coach, analyze, games, puzzles, waitlist, auth)
**Components:** 8 custom + 15 shadcn/ui
**Libs:** 10 (chess-engine, analysis-engine, game-storage, lichess, puzzle-bank, achievements, repertoire-storage, rate-limit, auth, utils)
**Tests:** 15 files, 131 tests, 100% pass, zero `as any` casts, zero TS errors
**Build:** ✅ Succeeds (needs machine restart if stale Node processes)
**Key decisions:** Dark emerald theme, Lichess API, free beta (no Stripe), minimax AI depth 4, localStorage persistence
**Next step:** Restart machine, `next build`, deploy to Vercel

---

## 🟢 AI Finance Brief

**Goal:** Daily AI-generated market summary delivered by email before market open
**Revenue:** $9/mo subscription (free delayed + Pro early delivery) — launching as free beta
**Repo:** github.com/kjhholt-alt/ai-finance-brief
**Local:** `C:\Users\Kruz\Desktop\Projects\Idea1`
**Stack:** Next.js 14, TypeScript, Tailwind, shadcn/ui, Claude API, Alpha Vantage, Resend, Vercel Cron

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Landing page + waitlist + deploy | ✅ Done | 2026-02-10 |
| 2 | Brief generation engine (market data + Claude) | ✅ Done | 2026-02-11 |
| 3 | Email delivery system (Resend + cron) | ✅ Done | 2026-02-11 |
| 4 | Auth + onboarding + settings page | ✅ Done | 2026-02-11 |
| 5 | Stripe payments & feature gating | ⏭ Skipped (free beta) | |
| 6 | Archive page + rating system + dashboard enhancements | ✅ Done | 2026-02-11 |
| 7 | TypeScript verification (zero errors) | ✅ Done | 2026-02-11 |
| 8 | SEO, legal pages, meta tags, sitemap | ✅ Done | 2026-02-11 |

**Pages:** 8 (landing, dashboard, archive, settings, onboarding, signin, terms, privacy)
**API Routes:** 8 (auth, brief, brief/archive, brief/[date], cron/send-brief, rating, user/preferences, waitlist)
**Brief pipeline:** Alpha Vantage → Claude API → 8-section JSON → cache → display + email
**Build:** ✅ Succeeds (~30s, 14 pages, 161 kB first load)
**Env needed:** Alpha Vantage key (free), Resend key, CRON_SECRET
**Next step:** Get API keys, deploy to Vercel, test with real market data

---

## 🟢 ClawBot Command Center

**Goal:** Admin dashboard + mobile command center for ClawBot AI agent infrastructure
**Repo:** github.com/kjhholt-alt/admin-dashboard
**Local:** `C:\Users\Kruz\Desktop\Projects\admin-dashboard`
**Stack:** Next.js 15, React 19, Supabase, Zustand, Tailwind, Lucide, Framer Motion
**Deploy:** ✅ **Vercel (`admin.buildkit.store`)** — LIVE

**Architecture (post "Go Lean" shift 2026-02-13):**
- **Primary desktop:** OpenClaw Gateway UI (`localhost:18789`)
- **Secondary mobile/remote:** This dashboard (`admin.buildkit.store`)
- **Task orchestration:** Claude Code Agent Teams (replaces custom TaskRouter)

**Routes (9 pages):**
| Route | Purpose |
|-------|---------|
| `/` | Landing: analytics, deployments, usage tracker, GitHub activity |
| `/login` | Password auth |
| `/mission-control` | Main dashboard: agent grid, command bar (Ctrl+K), task queue, alerts |
| `/mission-control/agent/[id]` | Agent detail: chat, metrics, task history |
| `/mission-control/chat` | ClawBot Prime dedicated chat interface |
| `/mission-control/projects` | Project selector & context management |
| `/mission-control/prompts` | Prompt template library (CRUD) |
| `/mission-control/alerts` | Escalation viewer (3-tier) |
| `/mission-control/settings` | Gateway URL config, Supabase status |

**Supabase tables:** 10 (agents, tasks, task_logs, agent_metrics, escalations, deploys, daily_reports, mc_projects, chat_messages, claude_usage_log)
**OpenClaw Gateway:** v2026.2.9, port 18789, model claude-sonnet-4-20250514, auto-start via Task Scheduler
**Unique features vs OpenClaw built-in:** Claude API cost tracking by model, persistent chat audit trail, project context injection, iOS/Android PWA, command palette
**20 commits in last 24 hours (Feb 12-13)** — very active development
**Known issues:** Skills not loading (need `clawhub install`), Vercel auto-deploy broken (manual workaround)

---

## 🟢 Outdoor CRM (AATOS)

**Goal:** Full CRM for All Around Town Outdoor Services — customers, jobs, estimates, invoices, routing
**Repo:** github.com/kjhholt-alt/outdoor-services-crm
**Local:** `C:\Users\Kruz\Desktop\Projects\outdoor-crm`
**Stack:** React 19 + Vite 5.4 + Tailwind 3.4 (frontend), Django 5 + DRF (backend), PostgreSQL
**Deploy:** ✅ **Vercel** (frontend) + **Railway** (backend) — BOTH LIVE

| Component | URL | Status |
|-----------|-----|--------|
| Frontend | `outdoor-services-crm.vercel.app` | ✅ Live |
| Backend | `motivated-vitality-production.up.railway.app` | ✅ Live |
| Health check | `/health/` | ✅ 200 OK |

**Pages (16):** Dashboard, Customers, Customer Detail, Customer Form, Jobs (FullCalendar), Job Form, Services, Estimates, Estimate Form, Invoices, Invoice Form, Reports, Reminders, Activities, Routes, Import/Export, Crew View, Market Leads

**11 major features:** FullCalendar (month/week/day), weather integration (7-day forecast), crew mobile view, photo documentation (IndexedDB), reports & analytics (5 Recharts), PDF invoice generation (jsPDF), toast notifications (Sonner), form pages with auto-fill, market leads (12 QC-area demos), customer redesign (avatars, status badges), demo data fallback

**Backend:** 7 Django apps, 5 service categories, 24 services, 40+ API endpoints, JWT auth
**Demo mode:** Comprehensive fallback data (~1000 lines) when backend unreachable
**Last updated:** Feb 13 (customer page redesign, demo mode fixes)
**Next:** Import real customer data, enforce auth, add custom domain

---

## 🟢 CRM-v2

**Goal:** Evolved version of the original CRM — modern, mobile-optimized
**Repo:** github.com/kjhholt-alt/CRM (or CRM-v2)
**Local:** `C:\Users\Kruz\Desktop\Projects\CRM-v2`
**Stack:** Django backend + React frontend, Supabase
**Last commit:** Feb 13 ("fix: build errors - unused imports and missing type")
**Status:** Active development, evolved from the older deployed CRM on Render

---

## 🟢 Website Factory

**Goal:** Config-driven website template system — one codebase, infinite client sites
**Revenue:** Per-client website fees ($500-2000) + monthly hosting
**Repo:** github.com/kjhholt-alt/ai-website-factory
**Local:** `C:\Users\Kruz\Desktop\Projects\website-factory`
**Stack:** Next.js 14, TypeScript, Tailwind, shadcn/ui, Prisma + SQLite/Postgres, NextAuth, React Hook Form + Zod, Resend

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Template system + first client site (Soccer Coach) | ✅ Done | 2026-02-12 |
| 2 | Forms API integration, validation, admin data wiring | ✅ Done | 2026-02-12 |
| 3 | Full admin CRUD, calendar events, CSV export, seed script | ✅ Done | 2026-02-12 |
| 4 | Email notifications + polish + deployment | ⬜ Not started | |
| 5 | Second client template (dental/restaurant) | ⬜ Not started | |
| 6 | N16 Soccer Training client branding & site overhaul | ✅ Done | 2026-02-13 |

**Pages:** 13 (7 public + 6 admin)
**API Routes:** 11
**Database:** 4 models (Registration, ContactSubmission, CalendarEvent, AdminUser)
**First client:** Elite Soccer Academy
**Build:** ✅ Passes (22/22 routes), zero TS errors
**Next step:** Deploy to Vercel, email notifications

---

## 🟢 N16 Soccer

**Goal:** Client website for N16 Soccer Training — generated from Website Factory
**Repo:** github.com/kjhholt-alt/ai-website-factory (same repo, client config)
**Local:** `C:\Users\Kruz\Desktop\Projects\n16-soccer`
**Stack:** Next.js 14, static deployment
**Deploy:** ✅ **Vercel (`n16-soccer.vercel.app`)** — LIVE
**Last commit:** Feb 13 ("Remove admin API routes and Prisma for static deployment")
**Status:** Deployed and working. Originally had Prisma + admin routes, stripped for clean static site.

---

## 🟡 Local Website Generator

**Goal:** Automated local business website generation + lead conversion system
**Local:** `C:\Users\Kruz\Desktop\Projects\local-website-generator`
**Stack:** Next.js 15, React 19, Supabase, Puppeteer, Nodemailer, Twilio, Sharp
**Features:** CSV import of businesses, automated email/SMS outreach, site generation
**Status:** Recently created (Feb 13), in progress
**Last commit:** Feb 13

---

## 🟢 PC Bottleneck Analyzer

**Goal:** Scan PC hardware, detect bottlenecks, AI-powered optimization recommendations
**Revenue:** Free tool → affiliate links + Pro tier for AI features later
**Repo:** github.com/kjhholt-alt/pc-bottleneck-analyzer
**Local:** `C:\Users\Kruz\Desktop\Projects\pc-bottleneck-analyzer`
**Stack:** Next.js 16, React 19, Tailwind v4, Recharts, Python (psutil/WMI scanner), Claude API (planned)
**Deploy:** ✅ **Vercel (`pc-bottleneck-analyzer.vercel.app`)** — LIVE

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Python system scanner | ✅ Done | 2026-02-12 |
| 2 | Web dashboard + bottleneck analysis | ✅ Done | 2026-02-12 |
| 3 | AI deep analysis + chat + BIOS guide | ⬜ Not started | |

**What's built:** Python scanner (CPU, GPU, RAM, storage, motherboard, OS, network, BIOS), web dashboard with JSON upload, demo mode, rule-based analysis, performance scoring (/100 with letter grade), 30+ hardware database entries, 4 dashboard tabs, dark theme with neon accents
**Next step:** Session 3 — AI-powered deep analysis with Claude API

---

## 🟢 Stock Breakout Alert System (StockApp)

**Goal:** Real-time stock monitoring for ATR consolidation breakout patterns
**Repo:** github.com/kjhholt-alt/StockApp
**Local:** `C:\Users\Kruz\Desktop\Projects\StockApp`
**Stack:** Django 5 + DRF, React 18 + Vite + Tailwind, Celery + Redis, yfinance, PostgreSQL
**Deploy:** ✅ **Railway** — RUNNING

**What's built:** ATR consolidation detection, volume spike analysis, breakout probability scoring (Low/Medium/High), Mag 7 + SPY/QQQ/IWM tracking, Celery scheduled tasks (6 PM fetch, 6:15 PM analysis, 30-min alerts), Docker Compose, management commands
**Last commit:** Feb 5 ("Simplify Railway config - use only Procfile")
**Next:** RVOL display, earnings calendar, watchlist tags, support/resistance AI, SMS/push

---

## 🟢 Developer Portfolio

**Goal:** Personal portfolio site to showcase projects
**Repo:** github.com/kjhholt-alt/portfolio
**Local:** `C:\Users\Kruz\Desktop\Projects\portfolio`
**Stack:** Next.js 15, React 19, TypeScript, Tailwind, framer-motion, next-themes
**Deploy:** Pending Vercel deploy

**Pages:** 4 (Home, Projects, Services, Contact)
**Components:** 7 (Navigation, Footer, ThemeProvider, ThemeToggle, AnimatedSection, ProjectCard, TechStack)
**Design:** Syne + Outfit + JetBrains Mono fonts, warm amber/gold accent, dark default, dot-grid patterns, scroll animations
**Build:** ✅ Passes (7/7 routes), zero TS errors
**Last commit:** Feb 12 ("Add real project screenshots and OG/social images")
**Next step:** Deploy to Vercel, wire up contact form (Resend)

---

## 🟢 Agent Mission Control

**Goal:** Dashboard to monitor Claude Code sessions, project status, and send alerts
**Repo:** github.com/kjhholt-alt/agent-mission-control
**Local:** `C:\Users\Kruz\Desktop\Projects\mission-control`
**Stack:** Python FastAPI + watchdog + psutil (port 9000), Next.js 14 + shadcn/ui (port 3010)
**Deploy:** Local only

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1-6 | Full build: monitoring, alerts, AI analysis, prompts, queue, settings | ✅ Done | 2026-02-12 |

**What's built:** Project scanning (detects stacks, reads STATUS.md), live session detection (Claude Code processes, CPU/memory), system stats, SSE updates, push notifications (ntfy.sh), AI project analysis (Claude), context export, prompt manager, session queue, settings page
**Note:** Partially superseded by ClawBot Command Center for daily use

---

## 🟡 Creative Assets Agent

**Goal:** AI-powered marketing asset generation for ClawBot (thumbnails, banners, GIFs, copy)
**Repo:** github.com/kjhholt-alt/creative-assets-agent
**Local:** `C:\Users\Kruz\Desktop\Projects\CreativeAgent`
**Stack:** Node.js 20, TypeScript, Claude API, Replicate, Puppeteer, FFmpeg, Sharp, Zod

**What's built:** Multi-agent orchestration architecture, service layer (Claude for copy, Replicate for images, Puppeteer for rendering, FFmpeg for video), Gumroad API integration
**Last commit:** Feb 12 ("Fix Replicate integration and complete first successful pipeline run")
**Blocker:** Anthropic API credit issues — needs workspace/org verification
**Next step:** Verify API key workspace, test Claude service standalone, then full pipeline

---

## 🟡 Trade Journal — SIDELINED

**Goal:** AI-powered trading journal with CSV import, pattern analysis, performance tracking
**Repo:** github.com/kjhholt-alt/trade-journal
**Local:** `C:\Users\Kruz\Desktop\Projects\Idea2`
**Stack:** Next.js 14 (frontend) + Python FastAPI (backend), Claude API, Recharts

**What's built:** Frontend (dashboard, CSV upload, AI analysis, 4 Recharts charts, auth, dark theme), Backend (trade upload, analysis, AI review endpoints, Dockerfile)
**Code:** ~961 lines total (594 frontend, 361 backend)
**Security issue:** ⚠️ Real Anthropic API key committed to git — needs rotation
**Decision:** Sidelined until Finance Brief + Chess Coach are live
**Next step (if resuming):** Rotate API key, deploy frontend to Vercel, backend to Railway

---

## 🟢 Gumroad Product Factory

**Goal:** One repo, one system, infinite digital products for Gumroad
**Revenue:** Direct product sales ($9-79 per product)
**Repo:** github.com/kjhholt-alt/gumroad-factory
**Local:** Work machine only (`C:\Users\GQETCUM\gumroad-factory`)
**Stack:** Markdown, TypeScript (validation scripts)

**Products:** 1 ready (AI Prompt Templates $24 PWYW), 9 planned
**Scripts:** validate-product.ts, package-product.sh, generate-listing.ts
**Next step:** Create cover image, upload to Gumroad

---

## 🟢 DayToDay

**Goal:** Daily productivity tool for Outlook, Teams, OneNote, Word aggregation
**Repo:** github.com/kjhholt-alt/daytoday
**Local:** Work machine only
**Stack:** Django 4.2 + DRF, React 19 + MUI 7, MSAL Python, SQLite
**Deploy:** Local (work tool, .bat launcher)

**What's built:** Outlook Calendar, Teams transcripts, OneNote capture, Word tracking, full-text search, daily summaries, people page, dark mode, PDF support, bulk import, PyInstaller packaging

---

## 🟡 GameDev / poe-assistant

**Goal:** Path of Exile 1 game assistant CLI tool
**Local:** `C:\Users\Kruz\Desktop\Projects\GameDev\poe-assistant`
**Stack:** Python 3.10+, Anthropic SDK, Click, Rich, HTTPX
**Status:** Alpha (v0.1.0)
**Purpose:** CLI tool for builds, Q&A, and game data

---

## 🟢 kitbuildshop-brand

**Goal:** Brand asset generation for KitBuildShop
**Local:** `C:\Users\Kruz\Desktop\Projects\kitbuildshop-brand`
**Stack:** Node.js, Puppeteer, HTML templates
**Output:** Logo (400x400 PNG), header image (1500x500 PNG)
**Status:** Complete — assets generated

---

## 📄 Project Docs Hub

**Repo:** github.com/kjhholt-alt/project-docs
**Local:** `C:\Users\Kruz\Desktop\Projects\project-docs`
**Purpose:** Central hub for syncing Claude Code context across sessions
**Contains:** This file (PROJECTS.md), IMPROVE.md, session PDFs, build plans

---

## 📄 WWI Documentary Project (codename: Verdun)

**Goal:** Long-form WWI documentary YouTube channel that reverse-engineers the
*craft* of premium WWII docs (Tom Hanks-narrated doc + Dan Carlin) — narration
tone, pacing, story selection — and rebuilds that **style** around original WWI
content. Copy the method/feel, never the script/footage/voice.
**Repo (planned):** github.com/kjhholt-alt/wwi-doc-engine
**Docs:** `project-docs/wwi-documentary/` (strategy, style teardown, AI pipeline,
episode template, build-session plan, ready-to-run prompts)
**Stack:** Python orchestration, Claude API (long context), Whisper (transcribe
references), neural TTS (draft VO), DaVinci Resolve (edit)
**Status:** 📄 Scaffold complete (Jun 20). Nothing built yet.

**How AI is used:** (1) Whisper transcribes the reference docs → (2) Claude tears
down the transcript into a measurable `STYLE_SPEC.md` + 12-point checklist →
(3) pipeline runs topic → sourced research → outline → narration in the learned
voice → production pack, with human checkpoints at research/script/final cut.

**Guardrails:** every historical claim is sourced (anti-hallucination prompts);
public-domain WWI visuals only; AI-generated b-roll must be labeled; mimic style
(not copyrightable), never reproduce their content.

**First episode (pipeline shakedown):** The Christmas Truce, 1914.
**Next step:** Session 1 — build the teardown harness (`transcribe.py` +
`teardown.py`). Kickoff prompt is in `wwi-documentary/04_SESSION_PLAN.md`.

---

## 📦 Archived / Empty Projects

| Project | Notes |
|---------|-------|
| **LearingChess** | Earlier chess app attempt (Django + React, Docker). No git repo. Superseded by AI Chess Coach. |
| **PathOfBuilding-repo** | Upstream Path of Exile build planner clone (Lua). Reference material for poe-assistant. |
| **spotify-visualizer** | Empty project skeleton, no code written. Abandoned. |
| **TrioMoeny** | Empty directory. |
| **GameDev/HighLevelGames** | Placeholder file only. |

---

## Deployment Summary

| Project | Platform | URL | Status |
|---------|----------|-----|--------|
| ClawBot Command Center | Vercel | `admin.buildkit.store` | ✅ Live |
| Outdoor CRM (frontend) | Vercel | `outdoor-services-crm.vercel.app` | ✅ Live |
| Outdoor CRM (backend) | Railway | `motivated-vitality-production.up.railway.app` | ✅ Live |
| N16 Soccer | Vercel | `n16-soccer.vercel.app` | ✅ Live |
| PC Bottleneck Analyzer | Vercel | `pc-bottleneck-analyzer.vercel.app` | ✅ Live |
| StockApp | Railway | Running | ✅ Live |
| AI Chess Coach | Vercel | — | ⬜ Pending |
| AI Finance Brief | Vercel | — | ⬜ Pending |
| Portfolio | Vercel | — | ⬜ Pending |
| Website Factory | Vercel | — | ⬜ Pending |
| Trade Journal | Vercel + Railway | — | ⏸ Sidelined |

---

## OpenClaw / ClawBot Infrastructure

- **Gateway:** OpenClaw v2026.2.9, port 18789, Windows native auto-start
- **Dashboard:** `http://localhost:18789/#token=clawbot-gw-2026` (primary desktop)
- **Mobile/Remote:** `https://admin.buildkit.store` (PWA)
- **Model:** anthropic/claude-sonnet-4-20250514 (gateway default)
- **Tailscale:** `spaceship-1` (100.77.94.85) for remote access
- **Agent Teams:** Claude Code experimental feature enabled

---

## Priority Order (Feb 13)

### Ship Now (ready to deploy)
1. **AI Chess Coach** — 131 tests, zero errors, just needs `next build` + Vercel deploy
2. **AI Finance Brief** — MVP complete, needs API keys + Vercel deploy
3. **Portfolio** — Built and tested, needs Vercel deploy + contact form

### Active Development
4. **ClawBot Command Center** — Live, rapid iteration (20 commits/day)
5. **Outdoor CRM** — Deployed, needs real data + auth enforcement
6. **CRM-v2** — Active rebuild of original CRM
7. **Local Website Generator** — New project, in progress

### Maintain / Enhance
8. **PC Bottleneck Analyzer** — Deployed, add AI analysis next
9. **StockApp** — Deployed, add RVOL + alerts
10. **Website Factory** — Template ready, deploy when client appears
11. **N16 Soccer** — Deployed, maintain

### On Hold
12. **Creative Assets Agent** — Fix API credit issue
13. **Trade Journal** — Resume after top 3 launch
14. **Gumroad Factory** — Create cover + upload when ready
15. **Mission Control** — Functional, mostly superseded by ClawBot dashboard

---

## Stats

- **Total projects:** 23 directories
- **Active git repos:** 16
- **Deployed & live:** 6 (ClawBot, Outdoor CRM x2, N16 Soccer, PC Bottleneck, StockApp)
- **Ready to deploy:** 3 (Chess Coach, Finance Brief, Portfolio)
- **In active development:** 4 (ClawBot, Outdoor CRM, CRM-v2, Local Website Gen)
- **Sidelined/archived:** 6
- **Work machine only:** 3 (Portfolio, PC Bottleneck, Gumroad Factory, DayToDay)

---

## Notes

- Some projects built on work machine (`C:\Users\GQETCUM\...`) — repos on GitHub but not all cloned to home
- Vercel auto-deploy from git push is broken for some projects — use manual `.git` hide workaround
- `NEXT_PUBLIC_*` env vars are baked at build time — changing them requires redeploy
- `next build` can hang on home machine when stale Node processes consume memory — restart or taskkill first
- Trade Journal has exposed API key in git history — rotate before resuming

---

## How to Use This File
1. After every Claude Code session, update the relevant project's status
2. When starting a chat in Claude.ai Projects, paste or upload this file for instant context
3. Keep it honest — mark things with accurate status indicators
4. Review weekly: are you spreading too thin? Focus on shipping what's built.
