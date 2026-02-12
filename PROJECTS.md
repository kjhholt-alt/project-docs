# Active Projects — Master Tracker
> Last updated: 2026-02-11 (overnight autonomous session)
> Update this file after every session. Paste into Claude.ai Projects for instant context sync.

---

## 🟢 AI Chess Coach
**Goal:** AI-powered chess improvement app with playable game, analysis, puzzles, coaching
**Revenue:** $7/mo subscription (free tier + Pro)
**Repo:** github.com/kjhholt-alt/ai-chess-coach
**Local:** C:\Users\Kruz\Desktop\Projects\idea3
**Stack:** Next.js, TypeScript, Tailwind, shadcn/ui, chess.js, react-chessboard, Lichess API, Claude API, Anthropic SDK

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Project setup & foundation | ✅ Done | 2026-02-10 |
| 2 | Basic structure (pages, API routes, components) | ✅ Done | 2026-02-10 |
| 3 | Playable chess game (AI opponent, minimax, save/resume) | ✅ Done | 2026-02-11 |
| 4 | Analysis engine + Claude AI coaching API | ✅ Done | 2026-02-11 |
| 5 | Puzzle trainer (3 modes, 60 puzzles, Elo tracking) | ✅ Done | 2026-02-11 |
| 6 | Dashboard, progress tracking, 28 achievements | ✅ Done | 2026-02-11 |
| 7 | Comprehensive testing (no new features) | ⬜ Not started | |
| 8 | Lichess game import + game history viewer | ✅ Done | 2026-02-11 |
| 9 | Opening explorer + repertoire builder + quiz | ✅ Done | 2026-02-11 |
| 10 | Stripe payments & launch prep | ⏭ Skipped (free beta) | |

**Pages:** 12 (landing, dashboard, play, games, games/[id], analysis, puzzles, history, history/[id], import, openings, progress, repertoire)
**API Routes:** 6 (auth, games, analyze, coach, puzzles, waitlist)
**Components:** 9 (Navbar, ChessBoard, PlayableChessBoard, AnalysisCard, CapturedPieces, ErrorBoundary, GameOverModal, MoveList, + shadcn/ui)
**Libs:** 9 (chess-engine, analysis-engine, game-storage, lichess, puzzle-bank, achievements, repertoire-storage, rate-limit, auth)
**Current blockers:** `next build` hangs due to stale Node processes (needs machine restart), TypeScript compiles clean
**Key decisions made:** Dark emerald theme, Lichess API for game import/puzzles, free beta (no Stripe yet), minimax AI (no Stockfish WASM), localStorage for persistence
**Next step:** Testing session, then deploy to Vercel
**Deploy URL:** [not yet deployed]

---

## 🟢 AI Finance Brief
**Goal:** Daily AI-generated market summary delivered by email before market open
**Revenue:** $9/mo subscription (free delayed brief + Pro early delivery)
**Repo:** github.com/kjhholt-alt/ai-finance-brief
**Local:** C:\Users\Kruz\Desktop\Projects\Idea1
**Stack:** Next.js, TypeScript, Tailwind, shadcn/ui, Claude API, Alpha Vantage, Resend, Vercel Cron

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
**Waitlist signups:** 0
**Current blockers:** Need to restart machine to free memory for `next build`, need Resend API key for email testing
**Next step:** Deploy to Vercel, test with real Alpha Vantage API key
**Deploy URL:** [not yet deployed]

---

## 🟡 Trade Journal
**Goal:** AI-powered trading journal with CSV import, pattern analysis, performance tracking
**Revenue:** TBD (sidelined for now)
**Repo:** github.com/kjhholt-alt/trade-journal
**Local:** C:\Users\Kruz\Desktop\Projects\Idea2
**Stack:** Next.js (frontend) + Python FastAPI (backend), TypeScript, Tailwind, shadcn/ui, Recharts, Claude API

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Full MVP build (frontend + backend) | ✅ Done | 2026-02-10 |
| 2 | Design polish + Stripe removal | ✅ Done | 2026-02-10 |

**Current blockers:** Sidelined — focus on Finance Brief + Chess Coach first
**Deploy URL:** [not yet deployed]

---

## 🟡 PC Bottleneck Analyzer
**Goal:** Scan PC hardware, detect bottlenecks, AI-powered optimization recommendations
**Revenue:** Free tool -> affiliate links + Pro tier for AI features later
**Repo:** [not created yet]
**Stack:** Python (scanner agent), Next.js (web dashboard), Claude API, WebSocket

| Session | Task | Status | Date |
|---------|------|--------|------|
| 1 | Python system scanner | ⬜ Not started | |
| 2 | Web dashboard + bottleneck analysis | ⬜ Not started | |
| 3 | AI deep analysis + chat + BIOS guide | ⬜ Not started | |
| 4 | Real-time monitoring dashboard | ⬜ Not started | |
| 5 | Benchmark & comparison engine | ⬜ Not started | |
| 6 | Testing, polish, packaging (.exe) | ⬜ Not started | |

**Current blockers:** None
**Deploy URL:** [fill when deployed]

---

## 🔵 Work Projects
**Repo:** [path/to/repo]
**Current focus:** [what you're working on]
**Last session:** [date — what was done]
**Next session:** [what to tackle]
**Blockers:** [any issues]

---

## 📊 Token Usage Strategy
**Daily target:** 2-3 sessions (mix of project building + refactors/docs/tests)
**Session prompts doc:** token-maximization-playbook.md
**Existing projects to refactor:** Stock app, Chess roguelike, CRM, Music visualizer, PoE assistant

---

## 💡 Ideas Backlog (Not Started)
- Trade Journal / Backtest Analyzer (sidelined — revisit after Finance Brief + Chess Coach are live)
- [add ideas here as they come up]

---

## 📝 How to Use This File
1. After every Claude Code session, update the relevant project's status
2. When starting a chat in Claude.ai Projects, paste or upload this file for instant context
3. Keep it honest — mark things 🔴 if stuck, don't pretend sessions went perfectly
4. Review weekly: are you spreading too thin? Focus on one project at a time.
