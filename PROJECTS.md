# Active Projects — Master Tracker
> Last updated: 2026-02-12
> Update this file after every session. Paste into Claude.ai Projects for instant context sync.
> GitHub: `kjhholt-alt` | 13 repos total

---

## All Repos Overview

| # | Repo | Status | Stack | Deploy | Last Push |
|---|------|--------|-------|--------|-----------|
| 1 | ai-chess-coach | 🟢 Feature-complete | Next.js 14, chess.js, Lichess, Claude | Vercel (pending) | Feb 12 |
| 2 | ai-finance-brief | 🟢 Feature-complete | Next.js 14, Claude, Alpha Vantage, Resend | Vercel (pending) | Feb 12 |
| 3 | portfolio | 🟢 Built | Next.js 15, Framer Motion | Vercel (pending) | Feb 12 |
| 4 | pc-bottleneck-analyzer | 🟢 Deployed | Next.js 16, Python scanner, Recharts | Vercel (live) | Feb 12 |
| 5 | StockApp | 🟢 Deployed | Django 5, React 18, Celery, yfinance | Railway | Feb 12 |
| 6 | ai-website-factory | 🟢 Built | Next.js 14, Prisma, Resend | Vercel | Feb 12 |
| 7 | creative-assets-agent | 🟡 Blocked | Node.js 20, Claude, Replicate, Puppeteer | CLI tool | Feb 12 |
| 8 | agent-mission-control | 🟢 Functional | FastAPI, Next.js 14, watchdog | Local | Feb 12 |
| 9 | gumroad-factory | 🟢 Ready | TypeScript scripts, ts-node | Gumroad (manual) | Feb 12 |
| 10 | trade-journal | 🟡 Sidelined | Next.js 14 + Python FastAPI, Claude | Not deployed | Feb 12 |
| 11 | daytoday | 🟢 Functional | Django 4.2, React 19, MUI 7, MSAL | Local (work) | Feb 11 |
| 12 | CRM | 🟢 Deployed | Django 4.2, React 18, Vite, JWT | Render | Jan 28 |
| 13 | project-docs | 📄 Reference | Markdown, PDFs | N/A | Feb 12 |

---

## 🟢 AI Chess Coach

**Goal:** AI-powered chess improvement app with playable game, analysis, puzzles, coaching
**Revenue:** $7/mo subscription (free tier + Pro)
**Repo:** github.com/kjhholt-alt/ai-chess-coach
**Local:** C:\Users\Kruz\Desktop\Projects\idea3
**Stack:** Next.js 14, TypeScript, Tailwind, shadcn/ui, chess.js, react-chessboard, Lichess API, Claude API

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Project setup & foundation | ✅ Done | 2026-02-10 |
| 2 | Basic structure (pages, API routes, components) | ✅ Done | 2026-02-10 |
| 3 | Playable chess game (AI opponent, minimax, save/resume) | ✅ Done | 2026-02-11 |
| 4 | Analysis engine + Claude AI coaching API | ✅ Done | 2026-02-11 |
| 5 | Puzzle trainer (3 modes, 60 puzzles, Elo tracking) | ✅ Done | 2026-02-11 |
| 6 | Dashboard, progress tracking, 28 achievements | ✅ Done | 2026-02-11 |
| 7 | Comprehensive testing (131 tests, 11 files, 100% pass) | ✅ Done | 2026-02-12 |
| 8 | Lichess game import + game history viewer | ✅ Done | 2026-02-11 |
| 9 | Opening explorer + repertoire builder + quiz | ✅ Done | 2026-02-11 |
| 10 | Stripe payments & launch prep | ⏭ Skipped (free beta) | |

**Pages:** 12 (landing, dashboard, play, games, games/[id], analysis, puzzles, history, history/[id], import, openings, progress, repertoire)
**API Routes:** 6 (auth, games, analyze, coach, puzzles, waitlist)
**Components:** 9 (Navbar, ChessBoard, PlayableChessBoard, AnalysisCard, CapturedPieces, ErrorBoundary, GameOverModal, MoveList, + shadcn/ui)
**Libs:** 9 (chess-engine, analysis-engine, game-storage, lichess, puzzle-bank, achievements, repertoire-storage, rate-limit, auth)
**Tests:** 11 files, 131 tests (libs + all 6 API routes), 100% pass, zero `as any` casts
**Current blockers:** `next build` hangs due to stale Node processes (needs machine restart)
**Key decisions made:** Dark emerald theme, Lichess API for game import/puzzles, free beta (no Stripe yet), minimax AI (no Stockfish WASM), localStorage for persistence
**Next step:** Restart machine, `next build`, deploy to Vercel
**Deploy URL:** [not yet deployed]

---

## 🟢 AI Finance Brief

**Goal:** Daily AI-generated market summary delivered by email before market open
**Revenue:** $9/mo subscription (free delayed brief + Pro early delivery)
**Repo:** github.com/kjhholt-alt/ai-finance-brief
**Local:** C:\Users\Kruz\Desktop\Projects\Idea1
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
**Current blockers:** Need to restart machine for `next build`, need Resend API key for email testing
**Next step:** Deploy to Vercel, test with real Alpha Vantage API key
**Deploy URL:** [not yet deployed]

---

## 🟢 Developer Portfolio

**Goal:** Personal portfolio site to showcase projects and attract freelance clients. Professional web presence for all other projects.
**Revenue:** Client acquisition funnel (not direct revenue)
**Repo:** github.com/kjhholt-alt/portfolio
**Local:** C:\Users\GQETCUM\portfolio (work machine — not cloned to home machine)
**Stack:** Next.js 15, TypeScript, React 19, Tailwind CSS, framer-motion, next-themes

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Full site build (4 pages, 7 components, dark/light theme, animations) | ✅ Done | 2026-02-12 |
| 2 | Vercel deployment + contact form backend + real images | ⬜ Not started | |

**Pages:** 4 (Home, Projects, Services, Contact)
**Components:** 7 (Navigation, Footer, ThemeProvider, ThemeToggle, AnimatedSection, ProjectCard, TechStack)
**Design:** Syne + Outfit + JetBrains Mono fonts, warm amber/gold accent (#e8a019) on dark, dot-grid patterns, scroll animations
**Data:** Centralized src/data/projects.ts — edit projects in one place
**Tests:** Production build passes (7/7 routes), zero TypeScript errors
**Current blockers:** None — ready for deployment
**Key decisions:** Amber/gold accent (avoids generic AI purple), Syne display font, framer-motion scroll animations, next-themes dark default, route layouts for per-page SEO
**Next step:** Deploy to Vercel, wire up contact form (Resend), add real project screenshots
**Deploy URL:** [not yet deployed]

---

## 🟢 PC Bottleneck Analyzer

**Goal:** Scan PC hardware, detect bottlenecks, AI-powered optimization recommendations
**Revenue:** Free tool -> affiliate links + Pro tier for AI features later
**Repo:** github.com/kjhholt-alt/pc-bottleneck-analyzer
**Local:** C:\Users\GQETCUM\Desktop\Projects\pc-bottleneck-analyzer (work machine — not cloned to home machine)
**Stack:** Next.js 16, React 19, Tailwind v4, Recharts, Python (scanner), Claude API (planned)
**Deploy:** Vercel (live) — pc-bottleneck-analyzer.vercel.app

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Python system scanner | ✅ Done | 2026-02-12 |
| 2 | Web dashboard + bottleneck analysis | ✅ Done | 2026-02-12 |
| 3 | AI deep analysis + chat + BIOS guide | ⬜ Not started | |
| 4 | Real-time monitoring dashboard | ⬜ Not started | |
| 5 | Benchmark & comparison engine | ⬜ Not started | |
| 6 | Testing, polish, packaging (.exe) | ⬜ Not started | |

**What's built:** Python scanner (CPU, GPU, RAM, storage, motherboard, OS, network, BIOS), web dashboard with drag-and-drop JSON upload, demo mode, rule-based bottleneck analysis, performance scoring (/100 with letter grade), hardware database (30+ CPUs/GPUs), prioritized recommendations (free > cheap > upgrades), 4 dashboard tabs, dark theme with neon accents, input validation, ARIA accessibility
**Next step:** Session 3 — AI-powered deep analysis with Claude API, chat follow-up, BIOS guide
**Deploy URL:** pc-bottleneck-analyzer.vercel.app

---

## 🟢 Stock Breakout Alert System (StockApp)

**Goal:** Real-time stock monitoring that detects ATR consolidation patterns preceding breakouts
**Repo:** github.com/kjhholt-alt/StockApp
**Local:** C:\Users\Kruz\Desktop\Projects\StockApp
**Stack:** Django 5 + DRF, React 18 + Vite + Tailwind, Celery + Redis, yfinance, PostgreSQL
**Deploy:** Railway (backend deployed)

**What's built:** ATR consolidation detection, volume spike analysis, breakout probability scoring (Low/Medium/High), tracked symbols (Mag 7 + SPY/QQQ/IWM), React frontend, Celery scheduled tasks (6 PM fetch, 6:15 PM analysis, 30-min alerts), API endpoints (dashboard, stocks, analysis, alerts, refresh), management commands, Docker Compose
**Future enhancements:** RVOL display, earnings calendar, watchlist tags, support/resistance AI, SMS/push, Bollinger/MACD, user auth + personal watchlists
**Deploy URL:** [Railway — running]

---

## 🟢 Website Factory

**Goal:** Config-driven website template system — one codebase, infinite client sites. Change a JSON config, entire site transforms. Foundation for a web development business.
**Revenue:** Per-client website fees ($500-2000 per site) + monthly hosting/maintenance
**Repo:** github.com/kjhholt-alt/ai-website-factory
**Local:** C:\Users\Kruz\Desktop\Projects\website-factory
**Stack:** Next.js 14, TypeScript, Tailwind, shadcn/ui, Prisma + SQLite/Postgres, NextAuth, React Hook Form + Zod, Resend

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Template system + first client site (Soccer Coach) | ✅ Done | 2026-02-12 |
| 2 | Forms API integration, validation, admin data wiring | ✅ Done | 2026-02-12 |
| 3 | Full admin CRUD, calendar events, CSV export, seed script | ✅ Done | 2026-02-12 |
| 4 | Email notifications + polish + deployment | ⬜ Not started | |
| 5 | Second client template (dental/restaurant) to prove config swap | ⬜ Not started | |
| 6 | Stripe payments & client billing | ⬜ Not started | |
| 7 | Testing, deployment, launch | ⬜ Not started | |

**Pages:** 13 (7 public + 6 admin)
**API Routes:** 11 (register, contact, waiver, admin/registrations, admin/events, admin/messages, admin/export, admin/profile, admin/password, auth)
**Components:** 21 (9 UI, 5 home, 3 layout, 2 forms, 2 admin actions)
**Database:** 4 models (Registration, ContactSubmission, CalendarEvent, AdminUser)
**First client:** Elite Soccer Academy (soccer coaching business)
**Tests:** Production build passes (22/22 routes), zero TypeScript errors
**Current blockers:** None — ready for deployment
**Key decisions:** Config-driven JSON template, SQLite dev / Postgres prod, NextAuth credentials, shadcn/ui, no Stripe for initial build
**Next step:** Session 4 — Email notifications, polish, Vercel deployment
**Deploy URL:** [not yet deployed]

---

## 🟡 Creative Assets Agent

**Goal:** AI-powered marketing asset generation (thumbnails, banners, GIFs, copy) for ClawBot
**Repo:** github.com/kjhholt-alt/creative-assets-agent
**Local:** C:\Users\Kruz\Desktop\Projects\CreativeAgent
**Stack:** Node.js 20, TypeScript, Claude API, Replicate, Puppeteer, FFmpeg, Sharp, Zod

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Scaffolding + service layer | ✅ Done | 2026-02-12 |
| 2 | Test pipeline end-to-end | ⬜ Blocked | |

**What's built:** Multi-agent orchestration architecture, service layer (Claude for copy, Replicate for images, Puppeteer for rendering, FFmpeg for video), Gumroad API integration, ClawBot integration protocol
**Blockers:** Anthropic API returns "credit balance too low" — likely wrong workspace/org. NVM PATH not persistent on Windows (needs `nvm use 20` per session).
**Next step:** Verify API key workspace, test Claude service standalone, then full pipeline

---

## 🟢 Agent Mission Control

**Goal:** Dashboard to monitor Claude Code sessions, project status, and send alerts
**Repo:** github.com/kjhholt-alt/agent-mission-control
**Local:** C:\Users\Kruz\Desktop\Projects\mission-control
**Stack:** Python FastAPI + watchdog + psutil (port 9000), Next.js 14 + shadcn/ui (port 3010)
**Deploy:** Local only

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Python status watcher + FastAPI + Next.js placeholder | ✅ Done | 2026-02-12 |
| 2 | Web dashboard UI with real-time updates | ✅ Done | 2026-02-12 |
| 3 | Alert system with ntfy.sh | ✅ Done | 2026-02-12 |
| 4 | Claude AI integration for project analysis | ✅ Done | 2026-02-12 |
| 5 | Prompt manager and session queue | ✅ Done | 2026-02-12 |
| 6 | Settings page, README, final verification | ✅ Done | 2026-02-12 |

**What's built:** Project monitoring (scans Projects dir, detects stacks, reads STATUS.md), live session detection (Claude Code processes, CPU/memory), system stats, real-time SSE updates, push notifications (ntfy.sh), AI project analysis (Claude), context export, prompt manager, session queue, settings page
**API:** /api/projects, /api/sessions, /api/stats, /api/events (SSE), /api/alerts, /api/prompts, /api/queue

---

## 🟢 Gumroad Product Factory

**Goal:** One repo, one system, infinite digital products. Each product gets its own directory with everything Gumroad needs — deliverables, sales copy, cover specs, metadata — all generated and validated systematically.
**Revenue:** Direct product sales via Gumroad ($9-79 per product)
**Repo:** github.com/kjhholt-alt/gumroad-factory
**Local:** C:\Users\GQETCUM\gumroad-factory (work machine — not cloned to home machine)
**Stack:** Markdown, Bash, TypeScript (validation scripts), Gumroad

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Full factory build + first product (AI Prompt Templates Pack) | ✅ Done | 2026-02-12 |
| 2 | Cover image, Gumroad upload, test purchase | ⬜ Not started | |
| 3 | Second product (Side Project Launch Checklist) | ⬜ Not started | |

**Products:** 1 ready to upload (AI Prompt Templates $24), 9 planned
**First product:** AI Prompt Templates Pack — 10 battle-tested developer prompts, 6,653 lines of content, $24 PWYW
**Scripts:** validate-product.ts (completeness checker), package-product.sh (ZIP packager), generate-listing.ts (listing preview)
**Docs:** Gumroad upload checklist, product ideas backlog (10 products ranked), pricing strategy framework
**Current blockers:** Cover image needs manual creation (Canva/Figma), then upload to Gumroad
**Key decisions:** $24 PWYW for first product, markdown format (model-agnostic), one repo for all products
**Next step:** Create cover image, upload to Gumroad, make test purchase
**Deploy URL:** [not yet on Gumroad]

---

## 🟢 CRM System

**Goal:** Customer relationship management with route optimization
**Repo:** github.com/kjhholt-alt/CRM
**Local:** C:\Users\Kruz\Desktop\Projects\CRM
**Stack:** Django 4.2 + DRF (backend), React 18 + Vite + Tailwind v4 (frontend), JWT auth, SQLite/PostgreSQL
**Deploy:** Render (deployed, last commit Jan 28)

**What's built:** Customer management (search, sort, paginate), activity logging (calls, emails, meetings, texts, cold calls, card drops), notes with version history, follow-up reminders (30-business-day default), dashboard (overdue, today, this week, 30-day views), route optimization (nearest-neighbor), import/export (Excel/CSV), dark mode, iPad/mobile optimized, Leaflet maps
**API:** Auth, Customers CRUD, Activities, Reminders, Routes, Import/Export

---

## 🟢 DayToDay

**Goal:** Daily productivity tool aggregating Outlook meetings, Teams transcripts, OneNote pages, and Word docs
**Repo:** github.com/kjhholt-alt/daytoday
**Local:** Work machine only (not cloned to home machine)
**Stack:** Django 4.2 + DRF (backend), React 19 + Material UI 7 (frontend), MSAL Python, SQLite
**Deploy:** Local (work tool, .bat launcher)

**What's built:** Outlook Calendar integration (Power Automate + COM + Graph API), Teams transcript retrieval, OneNote page capture (COM + PowerShell bridge), Word document tracking, searchable history (full-text search), daily summaries (markdown by date), people page, dark mode, PDF support, clickable notes with detail dialog, bulk import for historical calendar files, PyInstaller packaging (.exe)
**Key tech:** Microsoft Graph API v1.0, MSAL Python, Power Automate (New Outlook compat), win32 COM services

---

## 🟡 Trade Journal — SIDELINED

**Goal:** AI-powered trading journal with CSV import, pattern analysis, performance tracking
**Revenue:** TBD
**Repo:** github.com/kjhholt-alt/trade-journal
**Local:** C:\Users\Kruz\Desktop\Projects\Idea2
**Stack:** Next.js 14 (frontend) + Python FastAPI (backend), Claude API, Recharts

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Full MVP build (frontend + backend) | ✅ Done | 2026-02-10 |
| 2 | Design polish + Stripe removal | ✅ Done | 2026-02-10 |

**What's built:** Frontend (dashboard, CSV upload, AI analysis, 4 Recharts charts, auth, shadcn/ui dark theme), Backend (trade upload, analysis, AI review endpoints, Dockerfile)
**Decision:** Sidelined until Finance Brief + Chess Coach are live and validated.
**Deploy URL:** [not yet deployed]

---

## 🟢 ClawBot Command Center

**Goal:** Admin dashboard for ClawBot claw machine business
**Repo:** Not yet on GitHub
**Local:** C:\Users\Kruz\Desktop\Projects\admin-dashboard
**Stack:** Next.js 15, React 19, Supabase, Zustand, Vercel KV, Tailwind CSS
**Deploy:** Vercel (`admin.buildkit.store`)

**Key tech:** Next.js 15 with Turbopack, Supabase (database), Zustand (state), Vercel KV (caching), next-themes
**Status:** In development, no STATUS.md tracking yet
**Needs:** STATUS.md created, documentation of what's implemented vs. planned

---

## Reference: Project Docs Hub

**Repo:** github.com/kjhholt-alt/project-docs
**Local:** C:\Users\Kruz\Desktop\Projects\project-docs
**Purpose:** Central hub for syncing Claude Code context across sessions and machines
**Contains:** This file (PROJECTS.md), IMPROVE.md, Claude Agent Watch.pdf, Idea Hopper v1.pdf, PC Bottleneck session plan

---

## Non-Git Local Projects (older/experimental)

These exist in `C:\Users\Kruz\Desktop\Projects` but have no git repos:
- `GameDev/` — Contains `poe-assistant` and `HighLevelGames` subfolders
- `LearingChess/` — Earlier chess app attempt (Django + React, docker-compose)
- `spotify-visualizer/` — Frontend + backend, has CLAUDE.md, no git
- `MusicVisual/` — Empty
- `TrioMoeny/` — Empty
- `PathOfBuilding-repo/` — Upstream community fork (Path of Exile build planner, Lua/C), not personal

---

## Priority Order

1. **AI Chess Coach** — Feature-complete, 131 tests, deploy to Vercel ASAP
2. **AI Finance Brief** — Feature-complete, deploy to Vercel
3. **Portfolio** — Built, needs Vercel deploy + contact form
4. **PC Bottleneck Analyzer** — Deployed, continue with AI analysis (Session 3)
5. **Gumroad Factory** — First product ready, create cover + upload
6. **ClawBot Command Center** — Active development, separate track
7. **StockApp** — Deployed, add enhancements when time allows
8. **Website Factory** — Template ready, deploy when client appears
9. **Creative Assets Agent** — Fix API key issue, then test pipeline
10. **Mission Control** — Functional, local tool
11. **DayToDay** — Functional work tool, iterate as needed
12. **CRM** — Deployed, stable
13. **Trade Journal** — Sidelined

---

## Notes

- Some projects built on work machine (`C:\Users\GQETCUM\...`) — repos on GitHub but not cloned to home machine: `portfolio`, `pc-bottleneck-analyzer`, `gumroad-factory`, `daytoday`
- WoW repo exists on GitHub but is empty (no commits)
- `next build` hangs on home machine when too many Node processes consume memory — restart machine or taskkill stale processes first

---

## How to Use This File
1. After every Claude Code session, update the relevant project's status
2. When starting a chat in Claude.ai Projects, paste or upload this file for instant context
3. Keep it honest — mark things with accurate status indicators
4. Review weekly: are you spreading too thin? Focus on shipping what's built.
