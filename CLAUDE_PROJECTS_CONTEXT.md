# Project Context for Claude

## About Me

- **OS**: Windows 11, PowerShell / Git Bash
- **GitHub**: `kjhholt-alt`
- **Working directory**: `C:\Users\Kruz\Desktop\Projects`
- **Skill level**: Learning — explain things in plain language, be a teacher not just a coder
- **Goal**: Building monetizable MVPs. Free-during-beta model (no Stripe yet). Validate with users first, add payments later.

---

## Deployment Rules

| Project Type | Platform | Notes |
|---|---|---|
| Frontend (React, Next.js, Vite) | **Vercel** | Zero-config, just `git push`. No PORT binding needed. |
| Backend (Python, Node APIs, databases) | **Railway** | Always use `process.env.PORT` / `os.environ.get("PORT")`. Never hardcode ports. |
| CRM | **Render** | Django backend deployed to Render |

---

## Shared Tech Stack (Most Projects)

- **UI**: shadcn/ui, Lucide React, Tailwind CSS (dark themes)
- **Animations**: Framer Motion
- **Auth**: NextAuth.js with email magic links (Resend)
- **AI**: Anthropic Claude API
- **Design**: Dark mode default, glass morphism, gradient utilities, professional landing pages
- **Testing**: Vitest (Chess Coach), TypeScript strict mode

---

## All GitHub Repos (13 total, sorted by last push)

| # | Repo | Last Push | Type | Status | Deploy |
|---|------|-----------|------|--------|--------|
| 1 | creative-assets-agent | Feb 12 | Node.js CLI | Scaffolded, API key blocker | CLI tool |
| 2 | ai-chess-coach | Feb 12 | Next.js 14 | Feature-complete, 131 tests | Vercel (not yet) |
| 3 | ai-finance-brief | Feb 12 | Next.js 14 | Feature-complete | Vercel (not yet) |
| 4 | website-factory | Feb 12 | Next.js 14 | Built, 5 sessions | Vercel |
| 5 | portfolio | Feb 12 | Next.js 15 | Built, needs deploy | Vercel |
| 6 | pc-bottleneck-analyzer | Feb 12 | Next.js 16 + Python | Sessions 1-2 done | Vercel (live) |
| 7 | StockApp | Feb 12 | Django + React | Deployed | Railway |
| 8 | project-docs | Feb 12 | Docs hub | Reference | N/A |
| 9 | gumroad-factory | Feb 12 | TypeScript scripts | First product ready | Gumroad (manual) |
| 10 | agent-mission-control | Feb 12 | FastAPI + Next.js | 6 sessions done | Local |
| 11 | trade-journal | Feb 12 | Next.js + FastAPI | Sidelined | Not deployed |
| 12 | daytoday | Feb 11 | Django + React | Functional | Local (work tool) |
| 13 | CRM | Jan 28 | Django + React | Deployed | Render |
| — | WoW | Feb 10 | Lua addons | Empty repo | N/A |

---

## Project Details

---

### 1. AI Chess Coach (`idea3/`)

**Repo**: `kjhholt-alt/ai-chess-coach`
**Stack**: Next.js 14, chess.js, react-chessboard, Lichess API, Claude API, Vitest
**Deploy**: Vercel (not yet deployed)
**Status**: Feature-complete, 131 tests passing, zero TS errors — ready for deployment

#### What's Built
- Landing page (dark emerald theme, glassmorphism, hero, features, waitlist)
- Playable chess game — full AI opponent (minimax + alpha-beta pruning), PvP mode, 3 difficulty levels, undo, resign, save/resume to localStorage
- PlayableChessBoard — click-to-move + drag-and-drop, legal move highlighting, promotion UI, check highlighting
- Chess engine — minimax at configurable depth, piece-square tables, material counting
- Game import — fetch games from Lichess API, parse NDJSON, save to localStorage
- Game viewer — interactive board with move-by-move replay, keyboard navigation
- Game analysis — client-side analysis engine (depth 4 minimax), move classification (brilliant/great/good/inaccuracy/mistake/blunder), accuracy calculation, evaluation graph
- AI coaching — Claude API for post-game coaching feedback (`/api/coach`)
- AI game analysis — Claude API for multi-game pattern analysis (`/api/analyze`)
- Puzzle trainer — 3 modes (Daily 5, Endless, Theme-based), 60 curated puzzles, Elo rating tracking, hints
- Opening explorer — Lichess opening database integration, interactive board, win/draw/loss percentages, AI opening advice
- Repertoire builder — add lines, quiz mode, move tree visualization, accuracy tracking
- Dashboard — game stats, accuracy trends, weakness radar, training streaks
- Progress tracking — stat cards, accuracy trend chart, mistakes per game, theme mastery, puzzle rating history
- Achievements — 28 definitions, automatic unlock checking
- Game history — browsable list with win/loss/draw filter
- Waitlist API with rate limiting
- TypeScript zero errors

#### Test Coverage — 131 Tests, 100% Pass
| File | Tests |
|------|-------|
| chess-engine | 9 |
| analysis-engine | 6 |
| game-storage | 19 |
| puzzle-bank | 22 |
| lichess | 17 |
| rate-limit | 6 |
| api-analyze | 10 |
| api-coach | 15 |
| api-games | 11 |
| api-puzzles | 5 |
| api-waitlist | 11 |

#### What's Not Done
- `next build` not verified (stale Node processes — needs restart)
- Auth pages not created (works without auth for free beta)
- No Stripe payments (intentionally skipped)

#### Next Steps
1. Restart machine, run `next build`
2. Deploy to Vercel
3. Configure env vars: `ANTHROPIC_API_KEY`, `NEXTAUTH_SECRET`, `NEXTAUTH_URL`

---

### 2. AI Finance Brief (`Idea1/`)

**Repo**: `kjhholt-alt/ai-finance-brief`
**Stack**: Next.js 14, Claude API, NextAuth v5, Resend, Alpha Vantage
**Deploy**: Vercel (not yet deployed)
**Status**: Feature-complete (8/8 sessions), needs build + deploy

#### What's Built
- Landing page (Bloomberg-meets-SaaS: hero, how it works, sample brief, pricing tiers, FAQ, waitlist)
- Navigation — responsive desktop + mobile, auth-aware
- Auth — NextAuth v5 credentials provider
- Dashboard — market pulse, top movers, sector spotlight, non-obvious take, sector grid, thing to watch, calendar, outlook, data sources, rating widget
- Brief generation engine — Claude API + Alpha Vantage market data + mock fallbacks
- Market data module — SPY/QQQ/DIA quotes, top gainers/losers, pre-market data
- Daily caching — one brief per day, force refresh
- Brief archiving — saves to `data/briefs/` by date
- Archive page & API endpoint
- Rating system — star rating + optional feedback, JSON storage
- Rate limiting — 10 req/min on API routes
- Waitlist API — email collection with validation
- Onboarding flow — 3-step (user type, sector interests, summary)
- User preferences API — JSON file storage
- Settings page — account info, investor profile, sector selection, watchlist, email opt-in, timezone
- Email system — dark finance-themed HTML template, cron endpoint for daily delivery
- Legal pages — Terms of Service, Privacy Policy
- SEO — OpenGraph, Twitter cards, keywords, robots, auto-generated sitemap + robots.txt
- TypeScript zero errors

#### What's Not Done
- `next build` not verified (stale Node processes)
- Email delivery untested (needs Resend API key)
- Alpha Vantage API key set to "demo"
- Not deployed

#### Next Steps
1. Restart machine, run `next build`
2. Deploy to Vercel
3. Get free Alpha Vantage API key, test with real data
4. Test email delivery with Resend

---

### 3. Portfolio (`portfolio/`)

**Repo**: `kjhholt-alt/portfolio`
**Stack**: Next.js 15, React 19, Tailwind CSS, Framer Motion, next-themes, Vercel Analytics
**Deploy**: Vercel — `portfolio-kruz-holts-projects.vercel.app`
**Status**: Built (1 session), needs Vercel deploy + contact form backend
**Local**: NOT cloned locally (built on work machine)

#### What's Built
- Homepage with hero section (animated name, tagline, CTA buttons, scroll indicator)
- About blurb section with gradient text
- Featured projects grid (4 cards, auto-pulled from data file)
- Tech stack showcase (10 tools in hover-interactive grid)
- Hire me / contact CTA section
- Projects page with filterable grid (AI/ML, Web Apps, Tools categories)
- Services page (3 service cards: AI apps, web dev, client sites)
- Process timeline (Discovery > Design > Build > Launch)
- Contact page with form (name, email, message) + validation
- Sidebar with availability indicator, GitHub link, email link
- Sticky navigation with scroll-aware background blur
- Mobile hamburger menu with AnimatePresence transitions
- Dark/light theme toggle (next-themes, dark default)
- Footer with GitHub, email links, copyright
- Scroll-triggered animations (framer-motion)
- Custom accent color system (warm amber/gold #e8a019)
- Per-page SEO metadata, OpenGraph, Twitter cards
- Vercel Web Analytics tracking
- `next build` passes clean

#### What's Not Done
- Contact form is client-side only (needs Resend/Formspree backend)
- Testimonials are placeholder content
- Project screenshots use geometric placeholder icons
- No favicon or OG image

#### Next Steps
1. Deploy to Vercel
2. Wire up contact form to Resend
3. Add real project screenshots

---

### 4. PC Bottleneck Analyzer (`pc-bottleneck-analyzer/`)

**Repo**: `kjhholt-alt/pc-bottleneck-analyzer`
**Stack**: Next.js 16, React 19, Tailwind v4, Recharts, Python (scanner), Claude API (planned)
**Deploy**: Vercel (live) — `pc-bottleneck-analyzer.vercel.app`
**Status**: Sessions 1-2 done, improvement pass complete
**Local**: NOT cloned locally (built on work machine)

#### What's Built
- Python system scanner — detects CPU, GPU, RAM, storage, motherboard, OS, network, BIOS settings
- Web dashboard with drag-and-drop JSON upload
- Demo mode with realistic sample data
- Rule-based bottleneck analysis engine (CPU, GPU, RAM, storage, thermal, settings)
- Performance scoring system (/100 with letter grade A/B/C/D)
- Hardware comparison database (30+ CPUs and GPUs)
- Prioritized recommendations (free fixes > cheap fixes > upgrades)
- Four dashboard tabs: Overview, Bottleneck Analysis, Recommendations, Raw Data
- POST `/api/scan` endpoint for scanner-to-dashboard data flow
- Dark theme with neon accent colors (cyan/amber/red/green)
- Input validation & sanitization
- ARIA accessibility attributes
- Deployed on Vercel

#### Remaining Sessions
| Session | Goal | Status |
|---------|------|--------|
| 1-2 | Scanner + Dashboard | Done |
| 3 | AI deep analysis + chat + BIOS guide | Planned |
| 4 | Real-time monitoring (WebSocket + live charts) | Planned |
| 5 | Benchmark engine + comparison | Planned |
| 6 | Testing, packaging (.exe), landing page | Planned |

---

### 5. Stock Breakout Alert System (`StockApp/`)

**Repo**: `kjhholt-alt/StockApp`
**Stack**: Django 5 + DRF, React 18 + Vite, Celery + Redis, yfinance, PostgreSQL
**Deploy**: Railway (backend deployed)
**Status**: Deployed, functional

#### What's Built
- Real-time stock monitoring — detects ATR consolidation patterns preceding breakouts
- Tracked symbols: AAPL, MSFT, GOOGL, AMZN, META, NVDA, TSLA, SPY, QQQ, IWM
- ATR calculation (14-day EMA of True Range)
- Consolidation detection (3+ consecutive tight days)
- Volume analysis (20-day avg, spike = >1.5x)
- Breakout probability scoring (Low/Medium/High)
- React frontend with Tailwind CSS
- Celery scheduled tasks (6 PM fetch, 6:15 PM analysis, 30-min alert emails)
- API endpoints: dashboard, stocks, analysis, alerts, refresh
- Management commands: `fetch_prices`, `analyze_stocks`, `seed_stocks`
- Docker Compose setup

#### Future Enhancements (Planned)
- RVOL display, intraday range, earnings calendar
- Watchlist tags (Setting Up, Breaking Out, False Breakout)
- Support/resistance detection (AI)
- SMS + push notifications
- Bollinger Bands + MACD
- User auth + personal watchlists

---

### 6. Website Factory (`website-factory/`)

**Repo**: `kjhholt-alt/ai-website-factory`
**Stack**: Next.js 14, Prisma, Tailwind CSS, shadcn/ui, Framer Motion, React Hook Form + Zod, Resend
**Deploy**: Vercel
**Status**: Built (5 sessions), template product ready

#### What's Built
- Dynamic site generation from JSON config (`site.json`)
- Multi-step registration system (parent + player + emergency)
- Admin dashboard (registrations, waivers, email)
- Email notifications (Resend)
- Database seeding (Prisma)
- SEO optimization
- Legal pages (terms, privacy)
- Responsive design
- Client intake form template (INTAKE.md)
- Example config for Elite Soccer Academy

#### How It Works
1. Fill INTAKE.md with client info
2. Create/edit `site.json`
3. `npx prisma db push && npm run seed`
4. Deploy to Vercel
5. Configure custom domain

---

### 7. Creative Assets Agent (`CreativeAgent/`)

**Repo**: `kjhholt-alt/creative-assets-agent`
**Stack**: Node.js 20, TypeScript, Claude API, Replicate, Puppeteer, FFmpeg, Sharp
**Deploy**: CLI tool (local)
**Status**: Scaffolded, blocked on Anthropic API credits

#### What's Built
- Orchestrates AI-powered marketing asset generation (thumbnails, banners, GIFs, copy)
- Integrates with ClawBot Prime for multi-agent workflow
- Service layer: Claude API (copy/prompts), Replicate (image generation), Puppeteer (rendering), FFmpeg (video)
- Gumroad API for product listing uploads

#### Blockers
- Anthropic API returns "credit balance too low" — likely wrong workspace/org
- NVM PATH not persistent on Windows — needs `nvm use 20` per session

---

### 8. Agent Mission Control (`mission-control/`)

**Repo**: `kjhholt-alt/agent-mission-control`
**Stack**: Python FastAPI + watchdog + psutil (port 9000), Next.js 14 + shadcn/ui (port 3010)
**Deploy**: Local only
**Status**: 6 sessions complete, functional

#### What's Built
- Project monitoring (scans Projects dir, detects tech stacks, reads STATUS.md)
- Live sessions (detects Claude Code processes, CPU/memory usage)
- System stats (real-time CPU, memory, disk)
- Real-time updates (Server-Sent Events)
- Push notifications (ntfy.sh integration)
- AI project analysis (Claude API)
- Context export (for Claude.ai Projects)
- Prompt manager (save/organize prompts)
- Session queue (priority ordering)
- Settings page

#### Key API Endpoints
- `GET /api/projects` — List all projects
- `GET /api/projects/{name}` — Single project detail
- `GET /api/projects/{name}/analyze` — AI analysis
- `GET /api/projects/{name}/context` — Context export
- `GET /api/sessions` — Active Claude Code sessions
- `GET /api/stats` — System resource usage
- `GET /api/events` — SSE stream

---

### 9. Gumroad Product Factory (`gumroad-factory/`)

**Repo**: `kjhholt-alt/gumroad-factory`
**Stack**: TypeScript (scripts), ts-node
**Deploy**: Gumroad (manual upload)
**Status**: First product ready to upload
**Local**: NOT cloned locally (built on work machine)

#### What's Built
- Full factory repo structure (templates, scripts, docs, products)
- Validation script — checks title, description, pricing, tags, deliverables
- Packaging script — zips deliverables with version naming
- Listing generation script — previews listing content
- Documentation suite (Gumroad checklist, product ideas backlog, pricing strategy)
- First product: **AI Prompt Templates Pack** ($24 PWYW)
  - 10 prompt template files (200-400 lines each, real content)
  - Bonus cheat sheet
  - Customer-facing README
  - Complete Gumroad listing copy

#### Product Catalog
| # | Product | Status | Price |
|---|---------|--------|-------|
| 001 | AI Prompt Templates Pack | Ready to Upload | $24 |
| 002 | Next.js SaaS Starter Kit | Planned | $49-79 |
| 003 | Claude Code Workflow Guide | Planned | $19-29 |

#### Next Steps
1. Create cover image (Canva/Figma)
2. Upload to Gumroad
3. Test purchase flow

---

### 10. CRM System (`CRM/`)

**Repo**: `kjhholt-alt/CRM`
**Stack**: Django 4.2 + DRF (backend), React 18 + Vite + Tailwind v4 (frontend), JWT auth
**Deploy**: Render (deployed)
**Status**: Functional, deployed (last commit Jan 28)

#### What's Built
- Customer management (search, sort, paginate)
- Activity logging (calls, emails, meetings, texts, cold calls, card drops)
- Notes with version history
- Follow-up reminders (30-business-day default)
- Dashboard (overdue, today, this week, 30-day views)
- Route optimization (nearest-neighbor algorithm)
- Import/Export (Excel/CSV)
- Dark mode toggle
- iPad/mobile optimized (48px touch targets)
- Leaflet maps integration

#### API Endpoints
- Auth: login, refresh, me, toggle-dark-mode
- Customers: CRUD, notes, activities, reminders
- Activities: types, recent, by_type
- Reminders: list, overdue, today, week, snooze, complete, cancel
- Routes: optimize, complete_stop, skip_stop
- Import/Export: preview, execute, export

---

### 11. DayToDay (`daytoday/`)

**Repo**: `kjhholt-alt/daytoday`
**Stack**: Django 4.2 + DRF (backend), React 19 + Material UI 7 (frontend), MSAL Python, SQLite
**Deploy**: Local (work tool, desktop .bat launcher)
**Status**: Functional (10+ commits Feb 11), heavily iterated
**Local**: NOT in Projects folder (built on work machine)

#### What's Built
- Daily productivity tool that aggregates Outlook meetings, Teams transcripts, OneNote pages, and Word documents
- Outlook Calendar integration (Power Automate + COM + Graph API — three approaches tried)
- Teams transcript retrieval
- OneNote page capture (COM + PowerShell bridge)
- Word document tracking (scans directories for modified .docx)
- Searchable history — full-text search across all meetings, notes, documents
- Daily summaries — auto-generated markdown indexed by date
- People page (coworker tracking)
- Dark mode
- PDF support
- Clickable note content with detail dialog
- Bulk import for historical calendar files
- PyInstaller packaging (.exe)
- One-click launch via `.bat` file

#### Key Tech
- Microsoft Graph API v1.0 for calendar/OneNote
- MSAL Python for auth
- Power Automate as calendar source (New Outlook compatibility)
- COM Outlook as fallback
- win32 COM services

---

### 12. Trade Journal (`Idea2/`) — SIDELINED

**Repo**: `kjhholt-alt/trade-journal`
**Stack**: Next.js 14 (frontend) + Python FastAPI (backend), Claude API, Recharts
**Deploy**: Not deployed
**Status**: MVP built (2 sessions), sidelined

#### What's Built
- Frontend: Dashboard, CSV upload, AI analysis, 4 Recharts charts, auth, shadcn/ui dark theme
- Backend: Trade upload, analysis, AI review endpoints, Dockerfile
- Stripe removed, free beta branding

#### Decision
Sidelined until Finance Brief + Chess Coach are live and validated.

---

### 13. ClawBot Command Center (`admin-dashboard/`)

**Repo**: Not yet on GitHub
**Stack**: Next.js 15, React 19, Supabase, Zustand, Vercel KV, Tailwind CSS
**Deploy**: Vercel (`admin.buildkit.store`)
**Status**: In development, no STATUS.md tracking

#### Key Tech
- Next.js 15 with Turbopack
- Supabase for database
- Zustand for state management
- Vercel KV for caching
- next-themes for theme switching

---

### Non-Git Local Projects (older/experimental)

These exist in the Projects folder but have no git repos:
- `GameDev/` — Contains `poe-assistant` and `HighLevelGames` subfolders
- `LearingChess/` — Earlier chess app attempt (Django + React, has docker-compose)
- `MusicVisual/` — Empty
- `spotify-visualizer/` — Frontend + backend, has CLAUDE.md, no git
- `TrioMoeny/` — Empty
- `PathOfBuilding-repo/` — Upstream community fork (Path of Exile build planner, Lua/C), not a personal project

---

### Reference Repo: Project Docs Hub (`project-docs/`)

**Repo**: `kjhholt-alt/project-docs`
**Purpose**: Central hub for syncing Claude Code context across sessions
**Contains**: PROJECTS.md (master tracker), IMPROVE.md, PDFs (Claude Agent Watch, Idea Hopper, session plans)

---

## Priority Order

1. **AI Chess Coach** — Feature-complete, 131 tests, deploy to Vercel ASAP
2. **AI Finance Brief** — Feature-complete, deploy to Vercel after Chess Coach
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

## Code Discipline Rules

- **Run the app after every change** — verify it works before reporting back
- **Two-attempt debugging rule** — after 2 failed fixes, stop, summarize, and ask for context
- **Don't iterate on the same hypothesis** — if a fix direction isn't working, step back
- **Proactively suggest improvements** — but wait for approval before implementing
- **Self-serve installs** — if a CLI tool is needed, install it (except security-sensitive)
- **ONE task per session**, verify everything works before ending

## Design Rules

- shadcn/ui for all components (no raw HTML buttons/cards/inputs)
- Dark mode by default for finance apps
- Recharts for data visualization
- Clean, professional — "would a stranger trust this product and sign up?"

## Known System Issue

`next build` hangs on this Windows machine when too many Node processes are consuming memory. Solution: restart machine or `taskkill` stale Node processes before attempting builds. TypeScript compilation (`tsc --noEmit`) works fine.

## Notes

- Some projects were built on a work machine (different local path `C:\Users\GQETCUM\...`) — repos exist on GitHub under `kjhholt-alt` but aren't cloned to the home machine yet: `portfolio`, `pc-bottleneck-analyzer`, `gumroad-factory`, `daytoday`
- WoW repo is empty (no commits)
- PathOfBuilding-repo is an upstream community fork, not a personal build
