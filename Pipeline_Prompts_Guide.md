# Zero-Touch Pipeline Prompts — Selected Projects

## How to Use This Document

Each project below has a **single pipeline prompt** designed to be copy-pasted directly to Claude Code. The prompt contains everything Claude needs: context, tech stack, features, database schema, deployment plan, and success criteria.

**To run any of these:**
1. Create a new directory: `cd ~/Desktop/Projects && mkdir [project-name]`
2. Initialize: `cd [project-name] && git init`
3. Paste the prompt into Claude Code (CODE pane, Opus)
4. Let it run. Review output. Deploy.

**Or through the n8n pipeline:**
1. DM ClawBot the prompt
2. Approve the spec when it comes back
3. Walk away

---

## TABLE OF CONTENTS

1. [ReadMeNow — AI Code Documenter](#1-readmenow)
2. [QuestForge — AI Dungeon Master](#2-questforge) **[DONE]**
3. [ReplyBot — Local Business Review Response AI](#3-replybot)
4. [Archive Dormant Repos](#4-archive-dormant-repos)
5. [AI Finance Brief — Email Capture + Automated Delivery](#5-ai-finance-brief-email-system)
6. [BarrelHouse CRM Phase 3 Scoping](#6-barrelhouse-crm-phase-3)
7. [PL Engine — Full Hardening & Expansion](#7-pl-engine-hardening)
8. [PC Bottleneck Analyzer — PyInstaller EXE Lead Magnet](#8-pyinstaller-exe-scanner)
9. [MoneyPrinter — Performance Dashboard v2](#9-moneyprinter-dashboard-v2)
10. [buildkit.store/tools — Free Developer Utilities](#10-buildkit-tools)

---

<a name="1-readmenow"></a>
## 1. READMENOW — AI Code Documenter

### Context
A SaaS tool where developers paste a GitHub repo URL and get auto-generated documentation: README, API docs, architecture diagram, and CLAUDE.md. Solves a problem you face on every project.

### The Prompt

```
PROJECT: ReadMeNow — AI Code Documenter
REPO: Create new repo `readmenow` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

A SaaS web app where users paste a GitHub repo URL and get AI-generated documentation.

## TECH STACK (mandatory — do not deviate)
- Frontend: Next.js 16 + React + TypeScript
- Styling: Tailwind CSS + shadcn/ui (dark theme, rich gradients)
- Icons: Lucide React only
- Animations: Framer Motion (sparingly)
- State: Zustand (client) + React Query (server)
- Backend: Next.js API routes (no separate backend needed)
- Database: Supabase (auth, user data, generation history)
- AI: Claude API (anthropic SDK) — use claude-sonnet-4-5 for doc generation
- Deploy: Vercel
- Payments: Stripe Checkout

## CORE FEATURES (MVP)

### 1. Repo Input & Analysis
- Text input for GitHub repo URL (public repos only for MVP)
- "Analyze" button triggers the pipeline
- Loading state with progress steps: "Cloning repo... Analyzing structure... Reading code... Generating docs..."
- Use GitHub API to fetch repo tree, file contents, languages, README if exists
- Rate limit: GitHub API allows 60 req/hr unauthenticated, 5000 with token. Use user's GitHub OAuth token if available.

### 2. Document Generation (Claude API)
Generate these documents from a single repo analysis:

**README.md**
- Project name, description, badges (language, license, stars)
- Installation instructions (detect package.json → npm, requirements.txt → pip, Cargo.toml → cargo, etc.)
- Usage examples
- Configuration / environment variables (scan for .env.example or env var references)
- API documentation if applicable
- Contributing guidelines
- License

**ARCHITECTURE.md**
- High-level system overview
- Directory structure explanation
- Key modules and their responsibilities
- Data flow description
- Mermaid diagram of system architecture

**API_DOCS.md** (if API endpoints detected)
- Endpoint list with methods, paths, parameters
- Request/response examples
- Authentication requirements

**CLAUDE.md**
- Project context for AI assistants
- Tech stack summary
- Key patterns and conventions
- Deploy commands
- Known gotchas

### 3. Output Display
- Tabbed interface showing each generated document
- Syntax-highlighted markdown preview
- "Copy" button per document
- "Download All as ZIP" button
- "Push to Repo" button (creates a PR with the docs via GitHub API — requires OAuth)

### 4. User System
- Supabase Auth with GitHub OAuth (primary) + email/password (secondary)
- GitHub OAuth scopes: `repo` (for private repo access + PR creation), `read:user`
- Free tier: 3 generations per week
- Pro tier: Unlimited generations, private repo access, push-to-repo feature

### 5. Stripe Integration
- Pro plan: $9/month
- Stripe Checkout for subscription
- Webhook handler for subscription events (created, updated, canceled)
- Store subscription status in Supabase `profiles` table

### 6. Generation History
- Dashboard showing past generations with repo name, date, status
- Click to view/re-download past results
- Delete old generations

## DATABASE SCHEMA (Supabase)

```sql
-- Users (extends Supabase auth.users)
CREATE TABLE profiles (
  id UUID REFERENCES auth.users(id) PRIMARY KEY,
  github_username TEXT,
  github_token TEXT, -- encrypted, for API access
  stripe_customer_id TEXT,
  stripe_subscription_id TEXT,
  plan TEXT DEFAULT 'free' CHECK (plan IN ('free', 'pro')),
  generations_this_week INT DEFAULT 0,
  week_reset_at TIMESTAMPTZ DEFAULT now(),
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Generation history
CREATE TABLE generations (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES profiles(id),
  repo_url TEXT NOT NULL,
  repo_name TEXT NOT NULL,
  status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'analyzing', 'generating', 'complete', 'failed')),
  readme_md TEXT,
  architecture_md TEXT,
  api_docs_md TEXT,
  claude_md TEXT,
  mermaid_diagram TEXT,
  tokens_used INT,
  cost_usd FLOAT,
  error_message TEXT,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- RLS policies
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE generations ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users see own profile" ON profiles FOR ALL USING (auth.uid() = id);
CREATE POLICY "Users see own generations" ON generations FOR ALL USING (auth.uid() = user_id);
```

## CLAUDE API PROMPT (for doc generation)

System prompt for the generation call:
```
You are an expert technical writer generating documentation for a software project.
You will receive the full file tree, key file contents, and metadata for a GitHub repository.

Generate documentation that is:
- Concise and developer-friendly
- Accurate to the actual codebase (don't invent features)
- Well-structured with proper markdown formatting
- Includes Mermaid diagrams where helpful
- Written for a developer who will pick up this project in 6 months

For the CLAUDE.md file, write it as instructions for an AI coding assistant:
- What the project does in 2 sentences
- Tech stack with versions
- Key patterns and conventions used
- Deploy/run commands
- Environment variables needed
- Known gotchas or quirks
```

## PAGES / ROUTES

- `/` — Landing page (hero, features, pricing, CTA)
- `/app` — Main app (repo input, generation, results) — requires auth
- `/app/history` — Past generations
- `/pricing` — Pricing page with Stripe checkout
- `/auth/login` — GitHub OAuth + email login
- `/auth/callback` — OAuth callback handler
- `/api/generate` — POST: triggers generation pipeline
- `/api/webhooks/stripe` — Stripe webhook handler

## DESIGN

- Dark theme (#0a0a0f background, zinc-900 cards)
- Accent: cyan (#22d1ee) for primary actions
- Secondary: purple (#a855f7) for Pro features
- Font: Inter or system font stack
- shadcn/ui components throughout
- Framer Motion: fade-in on page load, tab transitions, progress animation
- Mobile responsive but desktop-primary

## ENVIRONMENT VARIABLES

```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
ANTHROPIC_API_KEY=
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=
NEXT_PUBLIC_APP_URL=https://readmenow.buildkit.store
```

## DEPLOYMENT

- Deploy to Vercel
- Custom domain: readmenow.buildkit.store (Cloudflare DNS)
- Supabase project: create new or use existing

## SUCCESS CRITERIA

1. Can paste a public GitHub URL and get all 4 documents generated
2. Documents are accurate and well-formatted
3. Auth works (GitHub OAuth)
4. Free tier limits enforced (3/week)
5. Stripe checkout works for Pro upgrade
6. Generation history saves and displays correctly
7. All generated docs are copyable and downloadable

## WHAT TO DO FIRST

1. Initialize Next.js 16 project with TypeScript, Tailwind, shadcn/ui
2. Set up Supabase tables + RLS
3. Build the landing page
4. Build auth flow (GitHub OAuth)
5. Build the core generation pipeline (GitHub API → Claude API → display)
6. Add generation history
7. Add Stripe integration
8. Deploy to Vercel
9. Create CLAUDE.md for this project
10. Commit and push to GitHub

After each major feature, run the app and verify it works. Don't stack untested code.
```

### Estimated Cost
- Claude API per generation: ~$0.05-0.15 (Sonnet, reading ~50 files)
- Build cost via pipeline: ~$10-20 in API credits
- Hosting: Free (Vercel + Supabase free tiers)

---

<a name="2-questforge"></a>
## 2. QUESTFORGE — AI Dungeon Master [DONE]

### Context
A text-based RPG powered by Claude with persistent world state, character progression, inventory, and combat. Claude narrates the story and manages game mechanics. Think AI Dungeon but with actual game systems underneath.

### The Prompt

```
PROJECT: QuestForge — AI Dungeon Master
REPO: Create new repo `questforge` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

A web-based text RPG where Claude acts as a dungeon master. Players type actions in natural language, Claude narrates results and manages game state. Persistent characters, inventory, combat, and world exploration.

## TECH STACK (mandatory)
- Frontend: Next.js 16 + React + TypeScript
- Styling: Tailwind CSS + shadcn/ui (dark fantasy theme)
- Icons: Lucide React
- Animations: Framer Motion (text reveal, combat effects)
- State: Zustand (game state client-side) + React Query
- Backend: Next.js API routes
- Database: Supabase (auth, game saves, character data)
- AI: Claude API (claude-sonnet-4-5 for gameplay, claude-opus-4-6 for world generation)
- Deploy: Vercel
- Payments: Stripe (optional, add later)

## CORE FEATURES (MVP)

### 1. Character Creation
- Name, race (Human, Elf, Dwarf, Orc, Halfling), class (Warrior, Mage, Rogue, Cleric, Ranger)
- Each race/class combo gets starting stats: STR, DEX, CON, INT, WIS, CHA (3d6 base + modifiers)
- Starting inventory based on class (Warrior: sword + shield, Mage: staff + spellbook, etc.)
- Brief backstory generated by Claude based on choices
- Character portrait: generate a text-art or emoji-based portrait (no image generation needed)

### 2. Game Interface
- Main area: scrollable story text with Claude's narration (styled like a book/parchment)
- Text input at bottom: player types actions ("I search the room", "Attack the goblin with my sword", "Cast fireball")
- Right sidebar: character sheet (stats, HP, inventory, gold, level, XP)
- Top bar: location name, time of day, weather
- Action buttons for common actions: Look, Inventory, Rest, Map, Save

### 3. Game Engine (Claude-managed with structured state)

Every player action sends the FULL game state to Claude along with the action. Claude returns:

```json
{
  "narration": "You swing your sword at the goblin...",
  "state_changes": {
    "hp_change": -5,
    "xp_gained": 25,
    "items_gained": ["rusty key"],
    "items_lost": ["health potion"],
    "gold_change": 10,
    "location": "goblin_cave_entrance",
    "quest_updates": [{"quest": "clear_the_cave", "status": "in_progress"}],
    "combat_active": false,
    "npcs_present": ["wounded merchant"],
    "time_advance": "30 minutes"
  },
  "available_actions": ["Talk to merchant", "Go deeper into cave", "Return to town", "Search bodies"]
}
```

The frontend parses this JSON and updates the UI. Claude ALWAYS returns structured JSON — the system prompt enforces this.

### 4. Combat System
- Turn-based when combat_active is true
- Player chooses: Attack, Defend, Cast Spell, Use Item, Flee
- Claude resolves actions using character stats (STR for melee, INT for magic, DEX for ranged/dodge)
- Damage = weapon_base + stat_modifier + d20 roll (Claude simulates dice)
- HP tracking for player and enemies
- Death = game over screen with option to load last save

### 5. World System
- Start in a small town (procedurally generated name by Claude)
- 5-6 locations to explore: town square, tavern, blacksmith, temple, forest edge, cave
- NPCs with names, personalities, and dialogue
- Day/night cycle affects encounters and NPC availability
- Quests: fetch quests, kill quests, escort quests (Claude generates contextually)

### 6. Progression
- XP from combat, quest completion, exploration
- Level up every 100 * current_level XP
- Level up grants: +HP, +stat points to allocate, new abilities unlocked
- Abilities by class (Warrior: Power Strike, Shield Bash; Mage: Fireball, Heal; etc.)

### 7. Save System
- Auto-save after every action (to Supabase)
- Manual save slots (3 per character)
- Load game from dashboard
- Multiple characters per account

### 8. User System
- Supabase Auth (email/password + anonymous play)
- Anonymous: can play but can't save (prompt to sign up to save)
- Registered: full save/load, multiple characters, history

## DATABASE SCHEMA

```sql
CREATE TABLE characters (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  name TEXT NOT NULL,
  race TEXT NOT NULL,
  class TEXT NOT NULL,
  level INT DEFAULT 1,
  xp INT DEFAULT 0,
  hp INT NOT NULL,
  max_hp INT NOT NULL,
  str INT NOT NULL, dex INT NOT NULL, con INT NOT NULL,
  int_stat INT NOT NULL, wis INT NOT NULL, cha INT NOT NULL,
  gold INT DEFAULT 50,
  inventory JSONB DEFAULT '[]'::jsonb,
  abilities JSONB DEFAULT '[]'::jsonb,
  active_quests JSONB DEFAULT '[]'::jsonb,
  completed_quests JSONB DEFAULT '[]'::jsonb,
  current_location TEXT DEFAULT 'town_square',
  world_state JSONB DEFAULT '{}'::jsonb,
  game_time TEXT DEFAULT 'morning',
  is_alive BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE game_logs (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  character_id UUID REFERENCES characters(id) ON DELETE CASCADE,
  player_action TEXT NOT NULL,
  narration TEXT NOT NULL,
  state_snapshot JSONB,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE save_slots (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  character_id UUID REFERENCES characters(id) ON DELETE CASCADE,
  slot_number INT NOT NULL CHECK (slot_number BETWEEN 1 AND 3),
  character_snapshot JSONB NOT NULL,
  log_snapshot JSONB NOT NULL,
  saved_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(character_id, slot_number)
);

ALTER TABLE characters ENABLE ROW LEVEL SECURITY;
ALTER TABLE game_logs ENABLE ROW LEVEL SECURITY;
ALTER TABLE save_slots ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users see own characters" ON characters FOR ALL USING (auth.uid() = user_id);
CREATE POLICY "Users see own logs" ON game_logs FOR ALL USING (
  character_id IN (SELECT id FROM characters WHERE user_id = auth.uid())
);
CREATE POLICY "Users see own saves" ON save_slots FOR ALL USING (
  character_id IN (SELECT id FROM characters WHERE user_id = auth.uid())
);
```

## CLAUDE API SYSTEM PROMPT

```
You are QuestForge, an expert fantasy dungeon master running a text-based RPG.

RULES:
1. ALWAYS respond with valid JSON matching the schema below. No prose outside the JSON.
2. Be dramatic, vivid, and immersive in narration. Use sensory details.
3. Respect the character's stats when resolving actions. A STR 8 Mage cannot kick down an iron door.
4. Combat uses simulated d20 rolls. State the roll result in narration ("You roll a 14...").
5. Be fair but challenging. Don't kill the player unfairly, but don't make it too easy.
6. Track quest state accurately. Don't forget ongoing quests.
7. NPCs have consistent personalities across interactions.
8. The world reacts to player choices. Stealing from the blacksmith makes him hostile.
9. If the player tries something impossible, narrate the failure humorously.
10. Suggest 3-4 available_actions but ALWAYS allow free-text input too.

RESPONSE FORMAT (strict JSON):
{
  "narration": "string — the story text the player reads",
  "state_changes": {
    "hp_change": number,
    "xp_gained": number,
    "items_gained": ["string"],
    "items_lost": ["string"],
    "gold_change": number,
    "location": "string or null if unchanged",
    "quest_updates": [{"quest": "string", "status": "new|in_progress|complete|failed"}],
    "combat_active": boolean,
    "enemy": {"name": "string", "hp": number, "max_hp": number} or null,
    "npcs_present": ["string"],
    "time_advance": "string",
    "level_up": boolean,
    "stat_increases": {"str": 0, "dex": 0, ...} or null,
    "new_abilities": ["string"] or null
  },
  "available_actions": ["string", "string", "string"]
}
```

## DESIGN

- Dark fantasy theme: deep purple/black backgrounds (#0d0117, #1a0a2e)
- Accent: gold (#fbbf24) for XP, actions, highlights
- Secondary: red (#ef4444) for combat, HP
- Tertiary: green (#22c55e) for healing, level ups
- Story text: serif font (Georgia or Merriweather) for immersion
- UI text: Inter/system sans-serif
- Character sheet: parchment-style card with stat bars
- Framer Motion: typewriter effect for narration reveal, shake on damage, glow on level up
- Mobile: story text fills screen, sidebar becomes bottom drawer

## PAGES

- `/` — Landing page (hero with fantasy art vibe, "Begin Your Quest" CTA)
- `/create` — Character creation wizard (step-by-step)
- `/play/[characterId]` — Main game interface
- `/dashboard` — Character list, save management
- `/auth/login` — Email login / sign up

## DEPLOY

- Vercel
- Domain: questforge.buildkit.store

## SUCCESS CRITERIA

1. Can create a character with race/class selection
2. Can type actions and get narrated responses
3. Game state persists (HP, inventory, gold, quests update correctly)
4. Combat works with turn-based resolution
5. Can save and load games
6. Story is engaging and responses feel like a real DM
7. Auth works, multiple characters per account

## BUILD ORDER

1. Next.js project + Tailwind + shadcn/ui setup
2. Supabase tables + auth
3. Character creation page
4. Core game loop: action input → Claude API → narration display → state update
5. Character sheet sidebar with live stats
6. Combat system
7. Save/load system
8. Landing page
9. Deploy to Vercel
10. CLAUDE.md + commit + push
```

### Estimated Cost
- Claude API per action: ~$0.01-0.03 (Sonnet, game state + action)
- Heavy session (100 actions): ~$1-3
- Build cost: ~$15-25
- Hosting: Free tier

---

<a name="3-replybot"></a>
## 3. REPLYBOT — Local Business Review Response AI

### Context
Connects to Google Business Profile. When a review comes in, Claude drafts a professional response. Owner approves with one click. Local businesses never respond to reviews — this fixes that and boosts their SEO.

### The Prompt

```
PROJECT: ReplyBot — Local Business Review Response AI
REPO: Create new repo `replybot` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

A SaaS that connects to a business's Google Business Profile, monitors for new reviews, and uses Claude to draft professional, personalized responses. Business owner approves/edits with one click.

## TECH STACK (mandatory)
- Frontend: Next.js 16 + React + TypeScript
- Styling: Tailwind CSS + shadcn/ui (dark theme)
- Icons: Lucide React
- Backend: Python FastAPI (for Google API integration + review polling)
- Database: Supabase
- AI: Claude API (claude-sonnet-4-5)
- Frontend deploy: Vercel
- Backend deploy: Railway
- Payments: Stripe
- Email: Resend (for notifications)

## CORE FEATURES (MVP)

### 1. Google Business Profile Integration
- OAuth2 with Google Business Profile API (My Business API)
- User connects their business listing
- Pull all existing reviews on first connect
- Poll for new reviews every 30 minutes (Railway cron or background task)
- Store reviews in Supabase

### 2. AI Response Generation
For each new review, Claude generates a response based on:
- Star rating (1-5)
- Review text content
- Business name and type
- Response tone preference (professional, friendly, casual)
- Business-specific context (menu items for restaurants, services for salons, etc.)

Rules:
- 5-star reviews: Thank them specifically for what they mentioned, invite them back
- 4-star reviews: Thank them, acknowledge any specific feedback, express desire to improve
- 3-star reviews: Apologize for not meeting expectations, address specific concerns, offer to make it right
- 2-star reviews: Sincere apology, address each concern, provide contact for resolution
- 1-star reviews: Professional apology, don't be defensive, offer direct contact to resolve
- NEVER be generic. Reference specific things the reviewer mentioned.
- Keep responses 2-4 sentences. Concise and professional.
- Never offer discounts or freebies in the response (liability).

### 3. Review Dashboard
- List all reviews sorted by newest first
- Each review card shows: reviewer name, star rating, review text, date, AI-drafted response
- Status: Pending → AI Draft Ready → Approved → Published
- One-click "Approve & Publish" sends response via Google API
- "Edit" button to tweak the draft before publishing
- "Regenerate" button for a new draft
- Bulk approve for 5-star reviews (common pattern: approve all 5-stars at once)

### 4. Settings & Customization
- Business type selection (restaurant, salon, dentist, auto shop, hotel, retail, other)
- Response tone slider (formal ↔ casual)
- Custom instructions ("Always mention our happy hour specials", "Never apologize for wait times on weekends")
- Excluded phrases ("We're sorry", "discount", etc.)
- Auto-publish toggle: automatically publish responses for 4-5 star reviews (opt-in)

### 5. Analytics
- Response rate (% of reviews with responses)
- Average response time
- Rating distribution chart
- Review volume over time
- Sentiment trend

### 6. Notifications
- Email (Resend) when new review arrives
- Email digest: daily summary of reviews + drafts waiting for approval
- Optional Discord webhook for real-time alerts

### 7. Pricing
- Free trial: 14 days, all features
- Starter: $29/mo — 1 location, up to 50 reviews/month
- Growth: $59/mo — 3 locations, unlimited reviews, auto-publish
- Agency: $149/mo — 10 locations, white-label option, priority support

## DATABASE SCHEMA

```sql
CREATE TABLE businesses (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  google_place_id TEXT NOT NULL,
  google_account_id TEXT,
  google_access_token TEXT,
  google_refresh_token TEXT,
  business_name TEXT NOT NULL,
  business_type TEXT DEFAULT 'other',
  response_tone TEXT DEFAULT 'professional',
  custom_instructions TEXT,
  excluded_phrases TEXT[],
  auto_publish_threshold INT, -- null = off, 4 = auto-publish 4-5 stars
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE reviews (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  business_id UUID REFERENCES businesses(id) ON DELETE CASCADE,
  google_review_id TEXT UNIQUE NOT NULL,
  reviewer_name TEXT,
  star_rating INT NOT NULL CHECK (star_rating BETWEEN 1 AND 5),
  review_text TEXT,
  review_date TIMESTAMPTZ,
  ai_response TEXT,
  final_response TEXT, -- after user edits
  status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'draft_ready', 'approved', 'published', 'skipped')),
  published_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE subscriptions (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  stripe_customer_id TEXT,
  stripe_subscription_id TEXT,
  plan TEXT DEFAULT 'trial' CHECK (plan IN ('trial', 'starter', 'growth', 'agency')),
  locations_limit INT DEFAULT 1,
  trial_ends_at TIMESTAMPTZ DEFAULT (now() + interval '14 days'),
  created_at TIMESTAMPTZ DEFAULT now()
);

ALTER TABLE businesses ENABLE ROW LEVEL SECURITY;
ALTER TABLE reviews ENABLE ROW LEVEL SECURITY;
ALTER TABLE subscriptions ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users see own businesses" ON businesses FOR ALL USING (auth.uid() = user_id);
CREATE POLICY "Users see own reviews" ON reviews FOR ALL USING (
  business_id IN (SELECT id FROM businesses WHERE user_id = auth.uid())
);
CREATE POLICY "Users see own subscription" ON subscriptions FOR ALL USING (auth.uid() = user_id);
```

## BACKEND (FastAPI on Railway)

```
replybot-api/
├── main.py
├── config.py
├── routers/
│   ├── google_auth.py    # OAuth2 flow for Google Business Profile
│   ├── reviews.py        # Fetch, store, respond to reviews
│   └── webhooks.py       # Stripe webhooks
├── services/
│   ├── google_client.py  # Google Business Profile API wrapper
│   ├── ai_responder.py   # Claude API response generation
│   ├── review_poller.py  # Background job to check for new reviews
│   └── notifier.py       # Resend email notifications
├── tasks/
│   └── poll_reviews.py   # Cron job: poll all connected businesses
└── requirements.txt
```

## PAGES

- `/` — Landing page (hero, social proof, pricing, CTA)
- `/dashboard` — Review feed with AI drafts
- `/dashboard/[businessId]` — Single business view
- `/dashboard/analytics` — Charts and metrics
- `/dashboard/settings` — Tone, instructions, auto-publish
- `/connect` — Google Business Profile OAuth flow
- `/pricing` — Plans + Stripe checkout
- `/auth/login`

## DESIGN

- Dark theme, professional feel (this is a business tool)
- Accent: blue (#3b82f6) for primary actions
- Star colors: gold for stars, green for positive sentiment, red for negative
- Review cards: clean, card-based layout with clear status badges
- Mobile: owners check reviews on phone — mobile-first dashboard

## DEPLOY

- Frontend: Vercel → replybot.buildkit.store
- Backend: Railway → replybot-api
- Cron: Railway cron job every 30 min for review polling

## SUCCESS CRITERIA

1. Can connect a Google Business Profile via OAuth
2. Reviews appear in dashboard
3. Each review gets an AI-drafted response
4. Can approve/edit/publish responses
5. Notifications work (email on new review)
6. Stripe payment flow works
7. Analytics show basic metrics

## BUILD ORDER

1. Next.js project + FastAPI project setup
2. Supabase tables + RLS
3. Google Business Profile OAuth integration
4. Review fetching and storage
5. Claude API response generation
6. Dashboard UI with review cards and approve/edit workflow
7. Notification system (Resend)
8. Stripe integration
9. Analytics page
10. Landing page
11. Deploy frontend (Vercel) + backend (Railway)
12. CLAUDE.md + commit + push
```

### Estimated Cost
- Claude API per response: ~$0.005 (Sonnet, short context)
- 50 reviews/mo: ~$0.25/mo in API costs
- Build: ~$20-30
- Hosting: ~$5-10/mo (Railway backend)

---

<a name="4-archive-dormant-repos"></a>
## 4. ARCHIVE DORMANT REPOS

### The Prompt

```
TASK: Archive dormant GitHub repos

Using the GitHub MCP server, archive the following repositories under the `kjhholt-alt` account:

1. creative-agent
2. lead-tracker
3. mission-control
4. jarvis-dashboard
5. CRM (the old duplicate of municipal-crm)
6. kitbuildshop-brand
7. game-tools
8. StockBot
9. KalshiBot
10. LiquidationBot

For each repo:
1. Use the GitHub MCP to set `archived: true` on the repository
2. If the repo doesn't have a description, add one: "Archived [date]. [brief description of what it was]."
3. Log which repos were successfully archived

If any repo name doesn't match exactly, list what you found and ask me to confirm.

Do NOT delete any repos. Archive only. They can be unarchived later.
```

### Notes
- This is a 5-minute task. No new code.
- Reduces your mental model from 20+ to ~10 active repos.
- Do this first before building anything new.

---

<a name="5-ai-finance-brief-email-system"></a>
## 5. AI FINANCE BRIEF — Email Capture + Automated Delivery

### The Prompt

```
PROJECT: AI Finance Brief — Add Email System
REPO: Existing repo `ai-finance-brief` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO ADD

Add email capture, subscriber management, and automated brief delivery to the existing AI Finance Brief app.

## CONTEXT
- This is an EXISTING Next.js 14 app deployed on Vercel
- Uses Claude API for AI-generated financial summaries
- Uses NextAuth for authentication
- Has Resend in the stack (MCP server available)
- Currently free-during-beta with no email delivery system

## FEATURES TO ADD

### 1. Email Capture Landing Section
- Add to the existing landing page or create a dedicated `/subscribe` page
- Email input + "Get Free Daily Brief" CTA
- Stores subscriber in Supabase `subscribers` table
- Double opt-in: sends confirmation email via Resend, subscriber clicks link to confirm
- Success message: "Check your inbox to confirm your subscription"

### 2. Subscriber Management (Supabase)

```sql
CREATE TABLE subscribers (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'confirmed', 'unsubscribed')),
  plan TEXT DEFAULT 'free' CHECK (plan IN ('free', 'premium')),
  stripe_customer_id TEXT,
  confirm_token UUID DEFAULT gen_random_uuid(),
  confirmed_at TIMESTAMPTZ,
  unsubscribed_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT now()
);
```

### 3. Brief Generation + Email Delivery (API Route or n8n trigger)
- Create `/api/send-brief` endpoint
- When called (via n8n cron or manual trigger):
  1. Generate today's market brief using Claude API (existing logic)
  2. Format as HTML email (clean, professional, financial newsletter style)
  3. Send to all confirmed subscribers via Resend
  4. Log delivery in Supabase `brief_logs` table
- Include "Unsubscribe" link in every email footer (required by law)
- Include "Upgrade to Premium" CTA in free tier emails

### 4. Unsubscribe Flow
- `/unsubscribe?token=[token]` — one-click unsubscribe
- Updates subscriber status to 'unsubscribed'
- Shows confirmation page: "You've been unsubscribed. Changed your mind? Re-subscribe."

### 5. Premium Tier Prep (Stripe)
- Add Stripe Checkout for $9.99/month premium plan
- Premium subscribers get: deeper analysis, portfolio-specific insights (future), earlier delivery
- Free tier: basic summary. Premium: full analysis with sector breakdowns and trade ideas.
- Stripe webhook handler for subscription management

### 6. Referral System (Simple)
- Each subscriber gets a unique referral link: `/subscribe?ref=[subscriber_id]`
- Track referrals in Supabase
- "Refer 3 friends, get premium free for a month" (implement the tracking, manual reward for now)

## DESIGN
- Email template: clean, dark-mode compatible, single-column, financial newsletter aesthetic
- Landing section: matches existing app design
- Include brief preview/sample on the landing page so people know what they're subscribing to

## DO NOT
- Do not rewrite the existing app
- Do not change the existing auth system
- Do not modify existing pages unless adding the email capture section
- Add these as NEW features layered on top

## SUCCESS CRITERIA
1. Can enter email → receive confirmation email → click confirm → status = confirmed
2. `/api/send-brief` generates and sends brief to all confirmed subscribers
3. Unsubscribe works
4. Stripe checkout works for premium upgrade
5. Referral links track correctly

## BUILD ORDER
1. Add Supabase tables
2. Email capture component + API route
3. Confirmation email flow (Resend)
4. Brief generation + email delivery endpoint
5. Unsubscribe flow
6. Stripe integration
7. Referral tracking
8. Test full flow
9. Commit + push (Vercel auto-deploys)
```

---

<a name="6-barrelhouse-crm-phase-3"></a>
## 6. BARRELHOUSE CRM PHASE 3 SCOPING

### The Prompt

```
TASK: Generate Phase 3 scope document and proposal for BarrelHouse CRM

## CONTEXT
- BarrelHouse CRM is a franchise CRM built with React 19 + Vite + Django 6
- Phase 2 is complete (auth with JWT + refresh tokens, Discovery Engine with 6 source types and AI scoring)
- Phase 3 was planned to include: Twilio SMS, Resend email, Celery drip campaigns
- This is CLIENT WORK — I need a professional proposal to send for invoicing

## GENERATE THE FOLLOWING

### 1. PHASE_3_PROPOSAL.md
A professional proposal document including:

**Executive Summary**
- What Phase 2 delivered (brief recap)
- What Phase 3 adds (communication layer: SMS, email, automated campaigns)
- Business value (automated lead nurture = higher conversion, less manual follow-up)

**Scope of Work**
- Twilio SMS integration: send/receive SMS from CRM, templates, bulk messaging
- Resend email integration: transactional emails, marketing emails, templates
- Celery + Redis drip campaign engine: define campaign sequences (Day 1: email, Day 3: SMS, Day 7: email), auto-trigger based on lead status changes
- Campaign analytics: open rates, click rates, response rates, conversion tracking
- Contact communication history: unified timeline showing all SMS, email, and notes per contact

**Technical Requirements**
- Celery worker + Redis (Railway deployment)
- Twilio API integration (SMS send/receive, webhook for incoming)
- Resend API integration (transactional + marketing)
- New Django models: Campaigns, CampaignSteps, CampaignEnrollments, Messages
- New React pages: Campaign builder (drag-and-drop step editor), Message templates, Communication log

**Timeline**
- Estimated: 2-3 weeks
- Milestone 1 (Week 1): SMS + email integration, send from CRM
- Milestone 2 (Week 2): Drip campaign engine with Celery
- Milestone 3 (Week 3): Campaign analytics + polish

**Pricing**
- [LEAVE BLANK — I'll fill in my rate]
- Structure: Fixed price per milestone, or hourly
- Payment terms: 50% upfront, 50% on delivery (suggested)

**Assumptions & Dependencies**
- Client provides Twilio account + credentials
- Client provides Resend account or approves Resend usage
- Redis instance on Railway (~$5/mo additional hosting)

### 2. PHASE_3_INVOICE_TEMPLATE.md
A simple invoice template I can fill in with actual numbers:
- Invoice number, date, due date
- Client: BarrelHouse Franchise
- Line items with descriptions and amounts
- Payment instructions
- Total

Format both documents professionally. Use markdown but structure them so I can easily convert to PDF.
```

---

<a name="7-pl-engine-hardening"></a>
## 7. PL ENGINE — Full Hardening & Expansion

### Context
This is your day job project. It needs to be bulletproof, well-documented, and expandable. This prompt goes beyond the two small fixes and treats it as the mission-critical enterprise tool it is.

### The Prompt

```
PROJECT: PL Engine — Full Audit, Hardening, and Documentation
REPO: Existing repo `pl-engine` in C:\Users\Kruz\Desktop\Projects\

## CONTEXT
This is a manufacturing overhead budget automation tool used at my DAY JOB. It replaces a 2-3 day manual Excel process with a 2-minute automated pipeline. It serves 5 factories and generates 19-sheet OH Review Packets and 19-slide PowerPoint presentations. It has 144 tests (highest in my portfolio).

THIS PROJECT MUST BE PERFECT. It affects real financial planning at a real company. Treat every change with the care of production enterprise software.

## PHASE 1: AUDIT & FIX (Do First)

### 1. JL01 PowerPoint Template Fix
- The JL01 factory PPTX template only has 5 slides (should have 19 like the other factories)
- Check `src/pptx_builder.py` (1248 lines) for how templates are loaded per factory
- Compare JL01 template against AX02/HX01/AX01 templates
- If the prior year deck is missing, create a template that matches the structure of the other factories
- Document what was wrong and what was fixed

### 2. PPTX Template Audit
- Run `scripts/audit_pptx_template.py --all`
- Document results for every factory
- Flag any template drift: slides that exist in some factories but not others
- Flag any hardcoded values that should be dynamic
- Create an audit report: `docs/PPTX_AUDIT_REPORT.md`

### 3. Test Suite Review
- Run all 144 tests. Document any failures.
- Check test coverage: which modules have tests, which don't?
- Identify the 5 highest-risk untested code paths (things that would break OH packets if wrong)
- Write tests for those 5 paths
- Target: 160+ tests after this phase

### 4. AX02 Calibration Check
- Engine produces $33.4M vs target $32.8M (1.7% variance, previously accepted)
- Document exactly where the variance comes from
- Is it rounding? A formula difference? A missing adjustment?
- Create `docs/AX02_CALIBRATION_NOTES.md` explaining the variance

## PHASE 2: DOCUMENTATION (Do Second)

### 5. Complete Technical Documentation
Create or update these docs:

**docs/ARCHITECTURE.md**
- System overview with data flow diagram
- Module responsibilities (oh_packet.py, pptx_builder.py, extractors, run.py)
- Factory-specific configurations
- Input file requirements (what Excel files it expects, where, in what format)
- Output file descriptions (what gets generated, where it goes)

**docs/FACTORY_GUIDE.md**
- Per-factory setup: AX02 (Valley City), HX01 (East Moline), AX01 (Moline), JL01 (Paton)
- Template locations
- Data source files
- Known quirks per factory
- How to add a NEW factory (step-by-step)

**docs/METHODOLOGY.md**
- The directs methodology: rate x hours for detail, interpolation for what-if
- Benefits engine logic
- Depreciation calculations
- Utilities handling
- Volume factor adjustments
- Inflation/merit escalation
- Where each methodology is implemented in code (file + function references)

**docs/RUNBOOK.md**
- How to run the engine for each factory
- Command examples with actual arguments
- Expected output for each run
- Troubleshooting: common errors and fixes
- How to update the engine when templates change (e.g., new fiscal year)

**docs/ONBOARDING.md**
- If someone else needs to use or maintain this tool, what do they need to know?
- Setup: Python environment, dependencies, network share access
- Quick start: run your first OH packet in 5 minutes
- FAQ

### 6. Update CLAUDE.md
- Full project context for any future Claude Code session
- All deploy/run commands
- All factory-specific gotchas
- References to all docs created above

## PHASE 3: HARDENING (Do Third)

### 7. Input Validation Layer
- Before processing ANY factory data, validate:
  - Required sheets exist in the input Excel file
  - Required columns exist in each sheet
  - No empty cells in critical fields (rates, hours, amounts)
  - Values are within reasonable ranges (no negative hours, no rates > $1000/hr)
- If validation fails: clear error message saying exactly what's wrong and which file/sheet/cell
- Log all validation results to `logs/validation_{factory}_{date}.log`

### 8. Error Handling Audit
- Review every file read/write operation — add try/except with meaningful error messages
- Review every Excel cell access — handle missing/empty cells gracefully
- Review every PPTX slide modification — handle missing chart data
- No silent failures. Every error should log what went wrong, which factory, which file, which cell.

### 9. Backup Before Overwrite
- Before generating ANY output file, check if a previous version exists
- If yes, copy it to `backups/{factory}/{filename}_{timestamp}` before overwriting
- Never lose a previously generated OH packet

### 10. Configuration Consolidation
- All factory-specific settings in one config file: `config/factories.yaml`
- Template paths, data source paths, output paths, factory codes, factory names
- No hardcoded factory names or paths scattered through the codebase
- If they're already centralized, verify and document. If not, consolidate them.

## PHASE 4: NICE-TO-HAVES (Only if Phases 1-3 are complete and clean)

### 11. Diff Report
- After generating a new OH packet, compare key figures against the previous version
- Output a simple diff: "AX02 Total OH: $33.4M → $33.6M (+0.6%)"
- Saves time during review — immediately see what changed

### 12. Email Delivery
- After generation, optionally email the OH packet + PPTX to a distribution list
- Use Resend or built-in smtplib
- CC yourself on every send

## RULES FOR THIS PROJECT

- DO NOT refactor working code for style reasons. If it works, document it and move on.
- DO NOT add features not listed above.
- Every change must pass ALL 144+ existing tests before committing.
- Test your changes with at least 2 factories (HX01 + AX02 recommended — most data)
- Commit after each completed phase with a clear commit message.
- If anything is unclear about the business logic, STOP and ask. Do not guess on financial calculations.

## SUCCESS CRITERIA

1. JL01 template produces a full 19-slide PPTX (matching other factories)
2. Template audit report covers all factories with clear status
3. 160+ tests, all passing
4. All 5 documentation files created and accurate
5. Input validation catches bad data before processing
6. No silent failures anywhere in the pipeline
7. Backup system prevents accidental overwrites
```

---

<a name="8-pyinstaller-exe-scanner"></a>
## 8. PC BOTTLENECK ANALYZER — PyInstaller EXE Lead Magnet

### The Prompt

```
PROJECT: PC Bottleneck Analyzer — Package Scanner as Downloadable EXE
REPO: Existing repo `pc-bottleneck-analyzer` in C:\Users\Kruz\Desktop\Projects\

## CONTEXT
The PC Bottleneck Analyzer has a Python scanner component that uses psutil, GPUtil, and WMI to detect hardware components. Currently it only runs locally during development. The goal is to package it as a standalone Windows EXE that users can download, run on their PC, and get results that point them to the website.

## WHAT TO BUILD

### 1. Standalone Scanner EXE
- Take the existing Python scanner code
- Package with PyInstaller as a single .exe file (--onefile flag)
- When run, it:
  1. Detects CPU (model, cores, threads, clock speed, utilization)
  2. Detects GPU (model, VRAM, utilization, driver version)
  3. Detects RAM (total, speed, slots used)
  4. Detects storage (type SSD/HDD, capacity, free space)
  5. Detects OS and system info
  6. Runs a quick benchmark: CPU multi-thread score, GPU capability estimate
  7. Identifies bottleneck: which component is weakest relative to others
  8. Generates a results summary in the terminal
  9. Opens the user's browser to: pcbottleneck.buildkit.store/results?cpu=X&gpu=Y&ram=Z&score=N
  10. The website receives the URL params and shows personalized upgrade recommendations with affiliate links

### 2. Results Landing Page
- Create `/results` page on the Next.js app
- Reads URL query params (cpu, gpu, ram, storage, score)
- Displays personalized bottleneck analysis
- Shows upgrade recommendations with Amazon affiliate links (tag: bottleneck20-20)
- CTA: "Want a more detailed analysis? Create a free account"
- Email capture: "Get your full report emailed to you"

### 3. Download Page
- Create `/download` page on the Next.js app
- Email gate: user enters email → gets download link
- Store email in Supabase `leads` table
- Immediate download link after email submission (don't make them check email)
- Also show direct download for people who don't want to give email (smaller, less prominent link)
- Track downloads in Supabase

### 4. PyInstaller Build Script
- Create `scripts/build_scanner.py` or `scripts/build_scanner.bat`
- Handles PyInstaller packaging with correct flags
- Output: `dist/PCBottleneckScanner.exe`
- Include version number in filename: `PCBottleneckScanner_v1.0.exe`
- Add a simple icon (create a .ico from the site's favicon)

## DATABASE ADDITIONS

```sql
CREATE TABLE leads (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  email TEXT NOT NULL,
  source TEXT DEFAULT 'scanner_download',
  scanner_version TEXT,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE scanner_results (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  lead_id UUID REFERENCES leads(id),
  cpu_model TEXT,
  gpu_model TEXT,
  ram_gb INT,
  storage_type TEXT,
  bottleneck_score INT,
  bottleneck_component TEXT,
  created_at TIMESTAMPTZ DEFAULT now()
);
```

## PYINSTALLER SPECIFICS
- Use `--onefile` for single EXE distribution
- Use `--noconsole` is NOT recommended — scanner should show a terminal with progress
- Hidden imports may be needed: `--hidden-import=psutil`, `--hidden-import=wmi`, `--hidden-import=GPUtil`
- Test on a clean Windows machine (or VM) to ensure all DLLs are bundled
- EXE size target: under 30MB if possible
- Add `--name=PCBottleneckScanner` flag

## SECURITY NOTES
- The EXE will trigger Windows SmartScreen warnings (unsigned)
- Add a note on the download page: "Windows may show a warning — click 'More info' → 'Run anyway'. This is normal for unsigned applications."
- Consider code signing in the future ($200-400/yr for a certificate)

## SUCCESS CRITERIA
1. PyInstaller builds a working .exe from the scanner
2. EXE runs on Windows, detects hardware, shows results in terminal
3. EXE opens browser to /results page with correct params
4. /results page shows personalized recommendations with affiliate links
5. /download page captures email before download
6. Leads table populates correctly
```

---

<a name="9-moneyprinter-dashboard-v2"></a>
## 9. MONEYPRINTER — Performance Dashboard v2

### The Prompt

```
PROJECT: MoneyPrinter — Dashboard v2 Performance Analytics
REPO: Existing repo `MoneyPrinter` in C:\Users\Kruz\Desktop\Projects\

## CONTEXT
MoneyPrinter has a Next.js 16 dashboard at `dashboard/` deployed on Vercel (moneyprinter-chi.vercel.app). It currently shows real-time data via WebSocket: KPI cards, P&L chart, whale summary, FV insight, builder rewards, deploy badge, and trades feed.

Current equity: ~$450 USDC. Bot is live and trading via whale copy strategy.

## WHAT TO ADD

### 1. Historical P&L Chart (Recharts)
- Daily P&L bar chart showing profit/loss per day since PNL_RESET_DATE (2026-02-24)
- Cumulative P&L line overlay
- Ability to toggle between: daily, weekly, all-time views
- Data source: query trades table in Supabase, aggregate by day
- Colors: green bars for profit days, red for loss days, cyan line for cumulative

### 2. Win Rate Analytics
- Overall win rate (% of profitable trades)
- Win rate by market duration (5min, 15min, 1hr, etc.)
- Win rate by whale tier (A vs B)
- Win rate by entry price range (<$0.10, $0.10-$0.30, $0.30-$0.50, >$0.50)
- Win rate by conviction score range (0-30, 30-60, 60-100)
- Display as a grid of metric cards with sparkline trends

### 3. Whale Performance Leaderboard
- Table showing each tracked whale's performance:
  - Wallet address (truncated), tier (A/B), trades copied, win rate, total P&L from their signals
- Sort by: P&L, win rate, trade count
- Highlight: best performer, worst performer
- Purpose: data to decide which whales to add/remove from tracking

### 4. Builder Rewards Tracker
- Chart showing weekly builder reward accruals over time
- Current week's estimated reward (based on volume)
- Cumulative total earned
- Volume chart: weekly trading volume (which drives rewards)
- Data source: builder_rewards table in Supabase

### 5. Risk Dashboard Panel
- Current wallet balance vs. wallet reserve ($60) vs. hard floor ($35)
- Remaining daily budget
- Current open positions count vs. max
- Drawdown from peak (%)
- Churn guard status (current cancel rate)
- All displayed as gauges or progress bars with color coding (green/amber/red)

### 6. Trade Log with Filters
- Full trade history table (paginated, 50 per page)
- Columns: date, market, direction, entry price, exit price, P&L, whale, conviction, FV action
- Filters: date range, profit/loss only, whale tier, market duration, min P&L
- Export to CSV button

## TECHNICAL NOTES
- All data comes from Supabase — query via existing Supabase client in the dashboard
- Use Recharts (already available) for all charts
- Use shadcn/ui tables for data tables
- Keep the existing real-time WebSocket feed — these are ADDITIONAL panels, not replacements
- Add a tab navigation: "Live" (existing dashboard) | "Analytics" (new panels) | "Trades" (trade log)
- Mobile responsive but desktop-primary

## DO NOT
- Do not modify the bot code (bot/ directory)
- Do not change existing dashboard panels — add new ones
- Do not change the WebSocket connection logic

## SUCCESS CRITERIA
1. Historical P&L chart renders correctly with real data
2. Win rate breakdowns show accurate stats
3. Whale leaderboard ranks whales by performance
4. Builder rewards show tracking over time
5. Risk dashboard shows current safety margins
6. Trade log filters work and CSV exports
7. Tab navigation between Live / Analytics / Trades works
8. All existing dashboard functionality still works

## BUILD ORDER
1. Add tab navigation to existing dashboard
2. Create Supabase query functions for aggregated analytics
3. Build Historical P&L chart
4. Build Win Rate analytics cards
5. Build Whale Leaderboard table
6. Build Builder Rewards chart
7. Build Risk Dashboard gauges
8. Build Trade Log with filters + CSV export
9. Test with live data
10. Commit + push (Vercel auto-deploys)
```

---

<a name="10-buildkit-tools"></a>
## 10. BUILDKIT.STORE/TOOLS — Free Developer Utilities

### The Prompt

```
PROJECT: buildkit.store Developer Tools Hub
REPO: Create new directory or add to existing buildkit.store project

## WHAT TO BUILD

A collection of free developer utility tools at buildkit.store/tools. Each tool is a single page. Purpose: drive SEO traffic to buildkit.store, cross-promote your other products (PC Bottleneck, AI Finance Brief, ReadMeNow, etc.), and establish buildkit.store as a useful brand.

## TECH STACK
- Next.js 16 + TypeScript
- Tailwind CSS + shadcn/ui
- All tools run 100% client-side (no backend needed, no API calls)
- Deploy: Vercel on buildkit.store

## TOOLS TO BUILD (10 tools, each is one page component)

### 1. JSON Formatter & Validator (`/tools/json`)
- Paste JSON → formats it with syntax highlighting
- Validates JSON and shows error location if invalid
- Minify / prettify toggle
- Copy formatted output button
- Tree view of JSON structure

### 2. Base64 Encode/Decode (`/tools/base64`)
- Text input → Base64 encoded output (and vice versa)
- File to Base64 (drag and drop)
- Auto-detect: is the input Base64 or plain text?

### 3. UUID Generator (`/tools/uuid`)
- Generate 1-100 UUIDs at once
- v4 (random) and v7 (timestamp-ordered) options
- Copy individual or copy all
- Bulk generate with one click

### 4. Color Palette Generator (`/tools/colors`)
- Input a hex color → generate complementary, analogous, triadic, split-complementary palettes
- Show each color with hex, RGB, HSL values
- Copy any value with one click
- Visual preview of the palette
- Tailwind CSS class output (closest Tailwind color)

### 5. Regex Tester (`/tools/regex`)
- Regex input + test string input
- Real-time match highlighting
- Show capture groups
- Common regex patterns sidebar (email, URL, phone, IP, date)
- Explain regex in plain English (parse the regex and describe what each part does — no AI needed, just regex parsing)

### 6. Markdown Preview (`/tools/markdown`)
- Split pane: markdown input on left, rendered preview on right
- Support GitHub Flavored Markdown
- Copy rendered HTML
- Download as .md file

### 7. JWT Decoder (`/tools/jwt`)
- Paste a JWT → decode header, payload, signature
- Show expiration time in human-readable format
- Highlight if expired
- Pretty-print the payload JSON
- DO NOT send the JWT anywhere — decode entirely client-side

### 8. Timestamp Converter (`/tools/timestamp`)
- Input: Unix timestamp (seconds or milliseconds) ↔ human date
- Show in multiple timezones (UTC, user's local, EST, PST, GMT)
- "Now" button to get current timestamp
- Relative time: "3 hours ago", "in 2 days"

### 9. Hash Generator (`/tools/hash`)
- Input text → generate MD5, SHA-1, SHA-256, SHA-512 hashes
- All computed client-side (Web Crypto API)
- Compare two hashes
- File hash: drag and drop a file to hash it

### 10. CSS Gradient Generator (`/tools/gradient`)
- Visual gradient builder: pick 2-4 colors, adjust angle/type (linear/radial)
- Real-time preview
- Copy CSS output
- Gallery of preset gradients
- Tailwind CSS output

## SHARED LAYOUT

### Tools Index Page (`/tools`)
- Grid of tool cards with icons, names, short descriptions
- Search/filter bar
- Each card links to the tool page

### Tool Page Layout
- Tool name + description at top
- The tool interface
- Sidebar or footer: "More tools from BuildKit" linking to other tools
- Footer CTA: "Check out our other products" → links to PC Bottleneck Analyzer, AI Finance Brief, etc.

### SEO
- Each tool page has unique meta title, description, keywords
- Format: "[Tool Name] — Free Online [Tool Type] | BuildKit Tools"
- Example: "JSON Formatter — Free Online JSON Validator & Prettifier | BuildKit Tools"
- Add structured data (JSON-LD) for each tool page
- Sitemap includes all tool pages

## DESIGN
- Dark theme consistent with buildkit.store branding
- Accent: cyan (#22d1ee) matching PC Bottleneck Analyzer
- Clean, minimal UI — the tool should be front and center
- shadcn/ui components for inputs, buttons, tabs
- Responsive: works on mobile but designed for desktop

## CROSS-PROMOTION
Every tool page includes a subtle banner or sidebar:
- "🔧 PC running slow? Try our free PC Bottleneck Analyzer"
- "📈 Stay on top of markets with AI Finance Brief"
- "📝 Generate docs for your repo with ReadMeNow"
Rotate which product is promoted based on the tool (dev tools → ReadMeNow, general → PC Bottleneck)

## SUCCESS CRITERIA
1. All 10 tools work correctly, entirely client-side
2. Tools index page shows all tools in a clean grid
3. Each tool has proper SEO meta tags
4. Cross-promotion links are present
5. Responsive and looks professional
6. Fast — no loading spinners, everything instant (client-side)

## BUILD ORDER
1. Set up the /tools route and layout
2. Build tools 1-3 (JSON, Base64, UUID — simplest)
3. Build tools 4-6 (Colors, Regex, Markdown — medium)
4. Build tools 7-10 (JWT, Timestamp, Hash, Gradient — medium)
5. Add SEO meta tags to every page
6. Add cross-promotion banners
7. Add sitemap entries
8. Deploy
9. Submit new pages to Google Search Console
```

---

## QUICK REFERENCE — COPY-PASTE ORDER

| # | Project | Type | Estimated Sessions | Priority |
|---|---------|------|--------------------|----------|
| 4 | Archive Dormant Repos | Cleanup | 5 min | Do first |
| 7 | PL Engine Hardening | Day job | 3-4 sessions | Do this week |
| 6 | BarrelHouse Phase 3 Scope | Client invoice | 1 session | Do this week |
| 9 | MoneyPrinter Dashboard v2 | Upgrade | 1-2 sessions | This week |
| 5 | AI Finance Brief Email | Upgrade | 1-2 sessions | This week |
| 8 | PyInstaller EXE Scanner | Lead magnet | 1 session | Next week |
| 10 | buildkit.store/tools | SEO play | 2 sessions | Next week |
| 1 | ReadMeNow | New SaaS | 2-3 sessions | Pipeline test |
| 3 | ReplyBot | New SaaS | 3-4 sessions | Pipeline test |
| 2 | QuestForge | Fun project | 2-3 sessions | When you want a break |

---

*Each prompt above is self-contained. Copy the code block, paste to Claude Code, let it run. Review the output. Deploy.*
