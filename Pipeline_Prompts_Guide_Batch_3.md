# Zero-Touch Pipeline Prompts — Batch 3

## 15 More Pipeline-Ready Projects

**Same rules as Batch 1 & 2:** Copy the code block, paste to Claude Code, let it run.

Add this to the top of every prompt:
```
## PIPELINE INSTRUCTIONS
- Use the GitHub MCP to create the repo and push all code
- Use the Supabase MCP to create all tables and RLS policies
- Use the Vercel MCP to verify deployment after pushing
- Run all tests before final commit
- Create CLAUDE.md as the last step
- Create .github/workflows/ci.yml with pytest/tsc checks on push
- After everything is built and tested, commit with a clear message and push to main
- Verify the Vercel deployment is live after push
```

---

## TABLE OF CONTENTS

1. [PitchCraft — AI Proposal & Scope Generator](#1-pitchcraft)
2. [DealFlow — Freelancer Deal Pipeline CRM](#2-dealflow)
3. [SnipVault — Code Snippet Manager](#3-snipvault)
4. [OGImage — Dynamic Open Graph Image API](#4-ogimage)
5. [FormForge — AI Form Builder](#5-formforge)
6. [DockLog — Container & Service Log Viewer](#6-docklog)
7. [LinkHive — Bookmark Manager with AI Tags](#7-linkhive)
8. [ClipDeck — Clipboard History Manager (Web)](#8-clipdeck)
9. [MeetBrief — AI Meeting Prep Tool](#9-meetbrief)
10. [EnvGuard — Environment Variable Manager](#10-envguard)
11. [PixelDiff — Visual Regression Testing Tool](#11-pixeldiff)
12. [SplitShip — Feature Flag Service](#12-splitship)
13. [HabitStack — Minimalist Habit Tracker](#13-habitstack)
14. [SLABot — SLA & Response Time Tracker](#14-slabot)
15. [PageSpeed — Website Performance Audit Tool](#15-pagespeed)

---

<a name="1-pitchcraft"></a>
## 1. PITCHCRAFT — AI Proposal & Scope Generator

### Why This One
You have 3 client projects (BarrelHouse, Outdoor CRM, N16 Soccer) and you're not invoicing them. This tool solves YOUR problem and then you sell it to every other freelancer.

### The Prompt

```
PROJECT: PitchCraft — AI Proposal & Scope of Work Generator
REPO: Create new repo `pitchcraft` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Describe a client project in 2-3 sentences → AI generates a full professional proposal with scope of work, timeline, pricing table, terms, and deliverables. Export as branded PDF. Track sent proposals and their status.

## TECH STACK (mandatory)
- Frontend: Next.js 16 + React + TypeScript
- Styling: Tailwind CSS + shadcn/ui (dark theme)
- Icons: Lucide React
- Animations: Framer Motion
- State: Zustand + React Query
- Backend: Next.js API routes
- Database: Supabase (auth, proposals, clients, templates)
- AI: Claude API (Sonnet 4.5 for generation)
- PDF: @react-pdf/renderer for PDF export
- Email: Resend for sending proposals
- Deploy: Vercel
- Payments: Stripe

## CORE FEATURES

### 1. Proposal Generator
- User inputs: client name, project description (free text, 2-5 sentences), estimated budget range, timeline preference, their hourly rate or project rate
- Claude generates:
  - **Executive Summary**: 3-4 sentences framing the problem and proposed solution
  - **Scope of Work**: Numbered deliverables with descriptions (e.g., "1. User Authentication System — JWT-based auth with email/password and Google OAuth, including forgot password flow")
  - **Timeline**: Milestone-based with week estimates per phase
  - **Pricing Table**: Line items with hours × rate, subtotal per phase, total project cost
  - **What's Included / Not Included**: Clear boundaries to prevent scope creep
  - **Terms & Conditions**: Payment schedule (50/50 or milestone-based), revision limits, IP ownership, cancellation policy
  - **Next Steps**: Clear CTA ("Sign below to proceed" or "Reply to this email to approve")
- User can edit ANY section before finalizing
- Save as template for similar future projects

### 2. Template System
- Pre-built templates: Web App, Mobile App, Landing Page, API/Backend, E-commerce, CRM, Dashboard
- Each template has smart defaults for scope items, timeline ranges, and pricing structures
- User can create custom templates from any proposal
- Templates remember your typical rates, terms, and preferred payment structures

### 3. Client Management
- Add clients: name, email, company, phone, notes
- See all proposals sent to each client
- Track: total proposed, total accepted, total revenue per client
- Quick "New proposal for [client]" button pre-fills their info

### 4. Proposal Tracking
- Status: Draft → Sent → Viewed → Accepted → Rejected → Expired
- Send via email (Resend) — includes PDF attachment + web view link
- Track when client opens the email and views the proposal
- Expiration date (default 14 days, configurable)
- One-click accept/reject for clients (web page with buttons)

### 5. PDF Export
- Professional branded PDF with:
  - Your logo (uploaded in settings)
  - Your business name, address, email, phone
  - Client's company info
  - All proposal sections formatted cleanly
  - Page numbers, date, proposal number (auto-incremented)
- Multiple PDF themes: minimal, corporate, creative
- Download or send directly

### 6. Analytics Dashboard
- Total proposals sent this month/quarter/year
- Win rate (accepted / total sent)
- Average deal size
- Revenue pipeline (sum of pending proposals)
- Time from sent to accepted (average)

### 7. Quick Estimate Mode
- For when you don't need a full proposal
- Client asks "how much for X?" → paste their message → Claude generates a quick 1-paragraph estimate with a range
- Copy-paste into email/Slack/Discord

## DATABASE SCHEMA

```sql
CREATE TABLE clients (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  name TEXT NOT NULL,
  email TEXT,
  company TEXT,
  phone TEXT,
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE proposals (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  client_id UUID REFERENCES clients(id),
  proposal_number TEXT NOT NULL, -- e.g., "PROP-2026-001"
  title TEXT NOT NULL,
  project_description TEXT NOT NULL,
  executive_summary TEXT,
  scope_items JSONB, -- [{title, description, hours, rate, subtotal}]
  timeline JSONB, -- [{phase, description, weeks, deliverables}]
  total_amount DECIMAL(10,2),
  payment_terms TEXT,
  inclusions TEXT[],
  exclusions TEXT[],
  terms_and_conditions TEXT,
  next_steps TEXT,
  status TEXT DEFAULT 'draft' CHECK (status IN ('draft', 'sent', 'viewed', 'accepted', 'rejected', 'expired')),
  pdf_url TEXT,
  sent_at TIMESTAMPTZ,
  viewed_at TIMESTAMPTZ,
  responded_at TIMESTAMPTZ,
  expires_at TIMESTAMPTZ,
  template_id UUID,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE templates (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  name TEXT NOT NULL,
  category TEXT, -- 'web_app', 'mobile', 'landing_page', etc.
  default_scope JSONB,
  default_timeline JSONB,
  default_terms TEXT,
  default_rate DECIMAL(10,2),
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE user_settings (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id) UNIQUE,
  business_name TEXT,
  logo_url TEXT,
  address TEXT,
  email TEXT,
  phone TEXT,
  default_rate DECIMAL(10,2),
  default_payment_terms TEXT DEFAULT '50% upfront, 50% on delivery',
  default_expiry_days INT DEFAULT 14,
  proposal_counter INT DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT now()
);

ALTER TABLE clients ENABLE ROW LEVEL SECURITY;
ALTER TABLE proposals ENABLE ROW LEVEL SECURITY;
ALTER TABLE templates ENABLE ROW LEVEL SECURITY;
ALTER TABLE user_settings ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users see own" ON clients FOR ALL USING (auth.uid() = user_id);
CREATE POLICY "Users see own" ON proposals FOR ALL USING (auth.uid() = user_id);
CREATE POLICY "Users see own" ON templates FOR ALL USING (auth.uid() = user_id);
CREATE POLICY "Users see own" ON user_settings FOR ALL USING (auth.uid() = user_id);
```

## CLAUDE API SYSTEM PROMPT

```
You are an expert freelance consultant who writes winning project proposals. Given a brief project description, generate a comprehensive proposal.

Rules:
- Be specific and detailed in scope items — don't use vague language like "build the frontend"
- Break deliverables into concrete, measurable items
- Timeline should be realistic (pad by 20% for buffer)
- Pricing should be transparent with per-phase breakdowns
- Inclusions/exclusions should prevent common scope creep scenarios
- Terms should protect the freelancer while being fair to the client
- Professional tone — confident, not salesy
- If the description mentions specific tech, use those. Otherwise default to modern web stack.

Output as JSON matching this schema:
{
  "executive_summary": "string",
  "scope_items": [{"title": "string", "description": "string", "hours": number, "rate": number}],
  "timeline": [{"phase": "string", "description": "string", "weeks": number, "deliverables": ["string"]}],
  "inclusions": ["string"],
  "exclusions": ["string"],
  "terms_and_conditions": "string",
  "next_steps": "string"
}
```

## PAGES

- `/` — Landing page (show a before/after: messy email → polished proposal)
- `/dashboard` — Proposal list + analytics
- `/proposals/new` — Create proposal (AI generation + editor)
- `/proposals/[id]` — View/edit proposal
- `/proposals/[id]/preview` — Public client view (with accept/reject buttons)
- `/clients` — Client list
- `/templates` — Template management
- `/settings` — Business info, logo, default rates
- `/pricing` — Stripe checkout
- `/auth/login`

## PRICING
- Free: 3 proposals/month, basic PDF template, no email sending
- Pro ($29/mo): Unlimited proposals, all PDF themes, email sending, tracking, templates, analytics
- Lifetime ($149): Everything forever

## DESIGN
- Dark theme (#0a0a0f)
- Accent: emerald (#10b981) — professional, money, trust
- Proposal preview: white background (like a real document), professional typography
- Dashboard: card-based with status badges and pipeline chart
- PDF: clean, corporate, would pass as coming from a real agency

## DEPLOY: Vercel → pitchcraft.buildkit.store

## SUCCESS CRITERIA
1. Can describe a project and get a complete proposal generated
2. Can edit any section of the generated proposal
3. PDF export looks professional
4. Can send proposal via email with tracking
5. Client can view and accept/reject via web link
6. Dashboard shows pipeline analytics
7. Templates work for repeat project types

## BUILD ORDER
1. Next.js + Tailwind + shadcn/ui + Supabase setup
2. Auth + user settings (business info, logo)
3. Client CRUD
4. AI proposal generation (Claude API)
5. Proposal editor (edit generated content)
6. PDF generation with @react-pdf/renderer
7. Proposal tracking (status, viewed_at)
8. Email sending via Resend
9. Client-facing view page with accept/reject
10. Templates system
11. Analytics dashboard
12. Stripe
13. Landing page
14. Deploy + CLAUDE.md

## FIRST USE: Generate proposals for BarrelHouse Phase 3, Outdoor CRM, and N16 Soccer. Send them. Get paid.
```

---

<a name="2-dealflow"></a>
## 2. DEALFLOW — Freelancer Deal Pipeline CRM

### Why This One
Lightweight CRM for tracking your client deals. You have active deals with BarrelHouse, Outdoor CRM, N16 Soccer — and no system to track them.

```
PROJECT: DealFlow — Freelancer Deal Pipeline CRM
REPO: Create new repo `dealflow` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

A kanban-style deal pipeline for freelancers. Track leads from first contact to paid invoice. Simpler than Salesforce, built for one-person operations.

## TECH STACK
- Next.js 16 + TypeScript, Tailwind + shadcn/ui, Supabase, Vercel, Stripe

## CORE FEATURES

1. **Kanban Board**: Columns: Lead → Contacted → Proposal Sent → Negotiating → Won → Lost → Completed. Drag-and-drop deals between stages. Each card shows: client name, deal value, days in stage, next action date.
2. **Deal Details**: Client info, deal value, source (referral, cold outreach, inbound), notes timeline (add dated notes), files (proposals, contracts), related emails (manual paste for now).
3. **Contact Management**: Name, email, phone, company, source, all deals associated. Quick add from deal creation.
4. **Activity Feed**: Global timeline of all actions: "Moved BarrelHouse CRM to Proposal Sent", "Added note to N16 Soccer", "Marked Outdoor CRM as Won ($X)".
5. **Revenue Dashboard**: Won deals this month/quarter/year. Pipeline value (sum of active deals). Win rate. Average deal size. Revenue by source (referral vs cold vs inbound). Monthly revenue chart.
6. **Reminders**: Set follow-up date per deal. Daily email digest of deals needing attention. Overdue follow-ups highlighted in red on the board.
7. **Quick Actions**: From the board: "Send follow-up email" (opens email compose), "Add note", "Change value", "Set next action date".

## DATABASE

```sql
CREATE TABLE contacts (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  name TEXT NOT NULL,
  email TEXT,
  phone TEXT,
  company TEXT,
  source TEXT CHECK (source IN ('referral', 'cold_outreach', 'inbound', 'repeat', 'other')),
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE deals (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  contact_id UUID REFERENCES contacts(id),
  title TEXT NOT NULL,
  value DECIMAL(10,2),
  stage TEXT DEFAULT 'lead' CHECK (stage IN ('lead', 'contacted', 'proposal_sent', 'negotiating', 'won', 'lost', 'completed')),
  source TEXT,
  probability INT DEFAULT 50,
  next_action TEXT,
  next_action_date DATE,
  won_at TIMESTAMPTZ,
  lost_at TIMESTAMPTZ,
  lost_reason TEXT,
  display_order INT DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE deal_notes (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  deal_id UUID REFERENCES deals(id) ON DELETE CASCADE,
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE activities (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  deal_id UUID REFERENCES deals(id) ON DELETE CASCADE,
  action TEXT NOT NULL, -- 'stage_change', 'note_added', 'value_changed', 'created', etc.
  details JSONB,
  created_at TIMESTAMPTZ DEFAULT now()
);
```

## PRICING
- Free: 10 active deals, basic board
- Pro ($15/mo): Unlimited deals, reminders, analytics, email digest, export

## PAGES
- `/` — Landing, `/board` — Kanban pipeline, `/deals/[id]` — Deal detail
- `/contacts` — Contact list, `/analytics` — Revenue dashboard
- `/settings` — Notification preferences

## DESIGN
- Dark theme, kanban columns with subtle color coding per stage
- Deal cards: compact, show key info at a glance
- Won deals: green accent, Lost: red, Active: cyan
- Drag handles, smooth Framer Motion transitions

## DEPLOY: Vercel → dealflow.buildkit.store

## BUILD ORDER
1. Setup + Auth + Supabase
2. Contact CRUD
3. Deal CRUD + kanban board (use @hello-pangea/dnd for drag-and-drop)
4. Deal detail page with notes timeline
5. Activity feed
6. Revenue analytics dashboard
7. Reminders + email digest (Resend)
8. Stripe
9. Landing page + deploy

## FIRST USE: Add your real deals: BarrelHouse Phase 3, Outdoor CRM, N16 Soccer, any MoneyPrinter consulting opportunities.
```

---

<a name="3-snipvault"></a>
## 3. SNIPVAULT — Code Snippet Manager

```
PROJECT: SnipVault — Personal Code Snippet Manager
REPO: Create new repo `snipvault` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Save, organize, search, and share code snippets. Syntax-highlighted, tagged, searchable. Like a personal Gist manager with better organization.

## TECH STACK
- Next.js 16 + TypeScript, Tailwind + shadcn/ui, Supabase, Vercel, Stripe

## FEATURES

1. **Create Snippet**: Title, language (auto-detect from code), code (Monaco editor or CodeMirror with syntax highlighting), description, tags. Support multi-file snippets (tabs).
2. **Organization**: Tags (multiple per snippet), folders/collections. Star favorites. Recent snippets quick access.
3. **Search**: Full-text search across title, description, code, and tags. Filter by language, tag, date. Instant results as you type.
4. **AI Features**: "Explain this snippet" (Claude summarizes what it does). "Improve this snippet" (Claude suggests optimizations). Auto-generate title and tags from pasted code.
5. **Share**: Public snippet link: snipvault.buildkit.store/s/[id]. Embeddable iframe widget for blog posts. "Copy as Gist" — exports to GitHub Gist via API.
6. **Import**: Import from GitHub Gist. Paste any code and auto-detect language.
7. **VS Code Extension** (future): Search and insert snippets from VS Code.

## DATABASE
- snippets (id, user_id, title, language, code TEXT, description, tags TEXT[], is_public BOOLEAN, is_starred BOOLEAN, collection_id UUID, view_count INT)
- collections (id, user_id, name, description, color)

## PRICING
- Free: 100 snippets, basic search
- Pro ($7/mo): Unlimited snippets, AI features, collections, public sharing, Gist sync

## DESIGN
- Dark theme with syntax-highlighting themes (Dracula, Monokai, One Dark, GitHub Dark)
- Monaco editor or CodeMirror 6 for the code input
- Snippet cards show first 5 lines of code with language badge
- Copy button on every snippet with "Copied!" animation

## DEPLOY: Vercel → snipvault.buildkit.store

## BUILD ORDER
1. Setup + Auth + Supabase
2. Snippet CRUD with Monaco/CodeMirror editor
3. Language auto-detection
4. Tags + collections + starring
5. Full-text search
6. Public sharing with syntax-highlighted view
7. AI features (explain, improve, auto-tag)
8. Import from Gist
9. Stripe
10. Landing page + deploy
```

---

<a name="4-ogimage"></a>
## 4. OGIMAGE — Dynamic Open Graph Image API

```
PROJECT: OGImage — Dynamic OG Image Generation API
REPO: Create new repo `ogimage` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

API that generates beautiful Open Graph images dynamically. Pass title, description, and style as URL params → get back a PNG. Used for: blog posts, SaaS pages, social cards, product pages. Every one of your products needs this.

## TECH STACK
- Frontend: Next.js 16 (landing + dashboard + docs), Vercel
- Image Gen: @vercel/og (Satori) — runs on Vercel Edge Functions, no separate backend needed
- Database: Supabase (API keys, usage tracking)
- Payments: Stripe

## FEATURES

1. **API Endpoint**: `GET /api/og?title=Hello+World&subtitle=My+Blog&theme=dark&accent=22d1ee`
   - Parameters: title (required), subtitle, theme (dark/light/gradient), accent (hex color), logo (URL), font (inter/mono/serif), template (blog/saas/product/social)
   - Returns: 1200x630 PNG (standard OG image size)
2. **Template Library**: 10 pre-built templates:
   - Blog post (title + author + date)
   - SaaS product (title + tagline + logo)
   - Social card (big text, gradient background)
   - GitHub-style (repo name + description + stats)
   - Conference talk (title + speaker + event)
   - Product launch (name + tagline + screenshot)
   - Podcast episode (show name + episode + guest)
   - Newsletter (title + edition number)
   - Tweet card (text on branded background)
   - Minimal (just title, clean typography)
3. **Visual Editor**: Drag-and-drop editor on the website to customize templates. Preview live. Get the API URL to use in your `<meta>` tags.
4. **Dashboard**: API key management. Usage stats. Favorite templates.
5. **Docs**: Interactive API docs with live preview for every parameter combination.
6. **Caching**: Same params = same image. Cache on Vercel Edge for 24h. Saves compute.

## DATABASE
- api_keys (id, user_id, key_hash, name, requests_this_month)
- og_templates (id, user_id, name, config JSONB, is_public BOOLEAN)
- usage_logs (id, api_key_id, template, params_hash, created_at)

## PRICING
- Free: 1,000 images/month, 5 templates, watermark
- Pro ($12/mo): 50,000 images/month, all templates, no watermark, custom templates, visual editor
- API: $0.001 per image after limit

## DESIGN
- Landing page: show a grid of example OG images generated by the tool
- Live playground: type a title → see the OG image update in real-time
- Dark theme dashboard

## DEPLOY: Vercel → ogimage.buildkit.store

## BUILD ORDER
1. Next.js setup + @vercel/og Edge Function for image generation
2. First 3 templates (blog, SaaS, social)
3. API with query params
4. Landing page with live playground
5. Dashboard + API key management
6. Visual editor
7. Remaining 7 templates
8. Caching layer
9. Stripe
10. Docs page + deploy

## FIRST USE: Generate OG images for pcbottleneck.buildkit.store, AI Finance Brief, and every other product. No more missing social cards.
```

---

<a name="5-formforge"></a>
## 5. FORMFORGE — AI Form Builder

```
PROJECT: FormForge — AI-Powered Form Builder
REPO: Create new repo `formforge` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Describe a form in plain English → AI generates it. "I need a client onboarding form that collects company name, project type, budget range, and timeline" → get a live form with validation, conditional logic, and a shareable link. Responses go to a dashboard or webhook.

## TECH STACK
- Next.js 16 + TypeScript, Tailwind + shadcn/ui, Supabase, Vercel, Claude API, Stripe

## FEATURES

1. **AI Form Generation**: Describe the form → Claude outputs a JSON schema defining fields, types, validation rules, and conditional logic. Supported field types: text, email, phone, number, textarea, select, multi-select, radio, checkbox, date, file upload, rating (1-5 stars), NPS (0-10), slider range.
2. **Form Editor**: Drag-and-drop field reordering. Click to edit any field (label, placeholder, required, validation). Add conditional logic: "Show field X if field Y = Z". Add page breaks (multi-step forms). Preview in real-time.
3. **Live Form**: Each form gets a public URL: formforge.buildkit.store/f/[form-id]. Beautiful, responsive, mobile-friendly. Progress bar for multi-step forms. Success message after submission (customizable).
4. **Response Dashboard**: Table of all submissions. Filter, sort, search. Export to CSV. Individual response detail view. Response analytics: completion rate, drop-off by field, average time to complete.
5. **Notifications**: Email notification per submission (Resend). Webhook URL (POST JSON to any endpoint — Slack, Zapier, n8n, etc.).
6. **Embeddable**: Embed form on any website via iframe or JS widget. Popup form triggered by button click.
7. **Templates**: Pre-built: Contact Us, Client Onboarding, Bug Report, Feature Request, Survey, Job Application, Event Registration, Feedback.

## DATABASE
- forms (id, user_id, title, description, fields_schema JSONB, settings JSONB, slug TEXT UNIQUE, is_active BOOLEAN, submission_count INT)
- submissions (id, form_id, data JSONB, metadata JSONB — IP, user agent, referrer, submitted_at)
- webhook_configs (id, form_id, url TEXT, is_active BOOLEAN)

## PRICING
- Free: 3 forms, 100 submissions/month, basic fields
- Pro ($19/mo): Unlimited forms, unlimited submissions, conditional logic, file uploads, webhooks, embed, analytics, templates

## DEPLOY: Vercel → formforge.buildkit.store

## BUILD ORDER
1. Setup + Auth + Supabase
2. AI form generation from text description
3. Form schema → live form renderer
4. Form editor with drag-and-drop
5. Submission storage + dashboard
6. Email notifications (Resend)
7. Webhook support
8. Embeddable widget/iframe
9. Templates
10. Analytics (completion rate, drop-off)
11. Stripe
12. Landing page + deploy
```

---

<a name="6-docklog"></a>
## 6. DOCKLOG — Service Log Viewer

```
PROJECT: DockLog — Multi-Service Log Aggregator
REPO: Create new repo `docklog` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Connect your Railway/Vercel/Supabase services → see all logs in one unified dashboard. Search, filter, tail in real-time. You currently check Railway logs, Vercel logs, and Supabase logs in 3 different browser tabs. This puts them all in one place.

## TECH STACK
- Frontend: Next.js 16 + TypeScript, Tailwind + shadcn/ui, Vercel
- Backend: Python FastAPI (log polling service), Railway
- Database: Supabase

## FEATURES

1. **Add Sources**: Connect log sources:
   - Railway: API token → fetch deployment logs via Railway API
   - Vercel: API token → fetch function logs and build logs
   - Generic webhook: any service can POST logs to your endpoint
   - Custom URL: poll any URL that returns log lines
2. **Unified Log View**: All logs from all sources in one scrollable, color-coded feed. Each log line shows: timestamp, source (Railway/Vercel/custom), service name, log level (info/warn/error), message. Color coding: blue=info, yellow=warn, red=error, gray=debug.
3. **Real-Time Tail**: WebSocket connection for live log streaming. Auto-scroll with pause button. New log counter when paused.
4. **Search & Filter**: Full-text search across all logs. Filter by: source, service, log level, time range. Regex search support. Save filter presets.
5. **Alerts**: Keyword alerts: "if log contains 'ERROR' or 'FATAL', send Discord notification". Rate alerts: "if more than 10 errors in 5 minutes, alert". Cooldown to prevent spam.
6. **Log Retention**: Store last 7 days of logs in Supabase. Older logs auto-deleted (cron job). Export any time range to CSV or JSON.

## DATABASE
- sources (id, user_id, name, type, config JSONB — API tokens, URLs, etc., is_active)
- log_entries (id, source_id, timestamp, level, message, metadata JSONB, created_at)
- alert_rules (id, user_id, name, condition JSONB, notification_channel, cooldown_seconds, last_triggered_at)

## PRICING
- Free: 2 sources, 3-day retention, no alerts
- Pro ($15/mo): Unlimited sources, 30-day retention, alerts, export, real-time tail

## DEPLOY: Frontend → Vercel, Backend → Railway

## BUILD ORDER
1. Setup + Auth + Supabase
2. Source management (add Railway/Vercel tokens)
3. Log polling service (FastAPI, fetches from Railway/Vercel APIs)
4. Unified log view with color coding
5. Search and filter
6. Real-time WebSocket tail
7. Keyword alerts + Discord notifications
8. Log retention cleanup cron
9. Stripe
10. Landing page + deploy

## FIRST USE: Add MoneyPrinter (Railway), pcbottleneck.buildkit.store (Vercel), admin dashboard (Vercel). All your logs in one place.
```

---

<a name="7-linkhive"></a>
## 7. LINKHIVE — Bookmark Manager with AI Tags

```
PROJECT: LinkHive — Smart Bookmark Manager
REPO: Create new repo `linkhive` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Save bookmarks → AI auto-tags and categorizes them → search and organize your internet brain. Chrome extension + web dashboard. Better than browser bookmarks because: searchable, tagged, cross-browser, shareable collections.

## TECH STACK
- Next.js 16 + TypeScript, Tailwind + shadcn/ui, Supabase, Vercel, Claude API, Stripe
- Chrome Extension: Manifest V3, vanilla JS or React

## FEATURES

1. **Save Bookmark**: Chrome extension button → saves current page with: URL, title, screenshot (via browser API), favicon. Or paste URL on web dashboard. AI auto-generates: tags (3-5), category, 1-sentence summary.
2. **Organization**: Auto-generated categories (Development, Design, Finance, News, etc.). Manual collections (like Pinterest boards). Tags — AI-generated + manual. Star favorites. Archive old bookmarks.
3. **Search**: Full-text across title, URL, tags, AI summary, and page content (we fetch and index the text). Instant results. Filter by category, tag, date range, domain.
4. **Collections**: Group bookmarks into shareable collections. Public collection URL: linkhive.buildkit.store/c/[slug]. "Best React Libraries 2026" — curate and share.
5. **Chrome Extension**: Small popup: shows "Save" button + quick tag editor. Keyboard shortcut: Ctrl+Shift+B to save instantly. Badge shows bookmark count.
6. **Import**: Import from Chrome bookmarks (HTML export), Pocket, Raindrop.io. Parse bookmark HTML file.
7. **Dashboard**: Grid or list view. Sort by: recently added, most visited, alphabetical. Daily/weekly digest email of saved bookmarks (remind you what you saved).

## DATABASE
- bookmarks (id, user_id, url TEXT, title TEXT, summary TEXT, tags TEXT[], category TEXT, favicon_url TEXT, screenshot_url TEXT, is_starred BOOLEAN, is_archived BOOLEAN, collection_id UUID, created_at)
- collections (id, user_id, name, slug UNIQUE, description, is_public BOOLEAN, created_at)

## PRICING
- Free: 100 bookmarks, basic tags, no AI
- Pro ($8/mo): Unlimited bookmarks, AI auto-tag, collections, search, Chrome extension, import/export

## DEPLOY: Vercel → linkhive.buildkit.store

## BUILD ORDER
1. Setup + Auth + Supabase
2. Bookmark CRUD (add by URL, auto-fetch title + favicon)
3. AI auto-tagging and categorization (Claude)
4. Search with full-text
5. Collections with public sharing
6. Chrome extension (Manifest V3)
7. Import from bookmark HTML
8. Dashboard views (grid/list, filters, sort)
9. Stripe
10. Landing page + deploy
```

---

<a name="8-clipdeck"></a>
## 8. CLIPDECK — Clipboard History Manager (Web)

```
PROJECT: ClipDeck — Web-Based Clipboard History
REPO: Create new repo `clipdeck` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

A web app that stores everything you copy. Search through clipboard history, pin frequently used snippets, organize into categories. Syncs across devices. Like Ditto or Alfred clipboard but web-based and cross-platform.

## TECH STACK
- Next.js 16 + TypeScript, Tailwind + shadcn/ui, Supabase (with Realtime for sync), Vercel, Stripe
- Chrome Extension for automatic capture

## FEATURES

1. **Auto-Capture**: Chrome extension listens for copy events → sends to Supabase. Also: manual paste into web dashboard for non-Chrome usage.
2. **Clipboard Feed**: Reverse-chronological list of everything copied. Each entry shows: text preview (first 100 chars), full content on click, timestamp, source URL (where you copied from). Content types: text, code (auto-detect + syntax highlight), URLs, images (clipboard images).
3. **Search**: Instant full-text search. Filter by: content type, date range, source domain.
4. **Pins**: Pin frequently used items to the top. Pin groups: "API Keys", "Email Templates", "Code Snippets", "Addresses".
5. **One-Click Copy**: Click any item → it's on your clipboard again. Keyboard shortcut navigation (arrow keys + Enter).
6. **Cross-Device Sync**: Supabase Realtime subscription. Copy on your desktop → available on your phone's browser instantly.
7. **Auto-Cleanup**: Auto-delete entries older than 30 days (configurable). Never store passwords (detect and skip entries that look like passwords — short, mixed chars, from login pages).
8. **Smart Suggestions**: AI detects repeated clips and suggests pinning them. Detect code → auto-format with syntax highlighting. Detect URL → show page title.

## DATABASE
- clips (id, user_id, content TEXT, content_type TEXT, source_url TEXT, source_title TEXT, is_pinned BOOLEAN, pin_group TEXT, created_at)

## PRICING
- Free: 500 clips, 7-day history, no sync
- Pro ($6/mo): Unlimited clips, 90-day history, cross-device sync, pins, Chrome extension, search

## DEPLOY: Vercel → clipdeck.buildkit.store

## BUILD ORDER
1. Setup + Auth + Supabase
2. Manual clip entry + feed display
3. Search + filtering
4. Pin system with groups
5. Chrome extension (copy event listener)
6. Cross-device sync (Supabase Realtime)
7. Auto-cleanup cron
8. Smart detection (code, URL, password skip)
9. Stripe
10. Landing page + deploy
```

---

<a name="9-meetbrief"></a>
## 9. MEETBRIEF — AI Meeting Prep Tool

```
PROJECT: MeetBrief — AI Meeting Preparation Assistant
REPO: Create new repo `meetbrief` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Paste a meeting invite or describe who you're meeting → AI researches the attendees and company, then generates a 1-page briefing doc: who they are, what they care about, potential talking points, recent news about their company, and questions to ask.

## TECH STACK
- Next.js 16 + TypeScript, Tailwind + shadcn/ui, Supabase, Vercel, Claude API (with web search tool), Stripe

## FEATURES

1. **Meeting Input**: Paste a calendar invite (parse attendees, company, meeting title). OR manually enter: attendee names, company, meeting purpose.
2. **AI Research + Brief Generation**: For each attendee, Claude (with web search enabled via API tools parameter) researches: LinkedIn profile summary, recent posts/activity, company news, role and responsibilities. Generates a 1-page brief:
   - **Meeting Context**: What this meeting is about (inferred from title/description)
   - **Attendee Profiles**: Name, title, key background, conversation starters
   - **Company Overview**: What they do, recent news, funding status, key products
   - **Talking Points**: 5 suggested topics based on their interests and your meeting purpose
   - **Questions to Ask**: 5 smart questions that show you did your homework
   - **Potential Objections**: If it's a sales/pitch meeting, what pushback to expect
3. **Brief Library**: Save all past briefs. Search by person, company, date. Revisit before follow-up meetings.
4. **Quick Prep**: "I'm meeting [name] from [company] in 10 minutes" → get a 3-bullet summary in 30 seconds.
5. **Export**: Copy as text, download as PDF, or share link.

## CLAUDE API CONFIG
Use Claude API with the web_search tool enabled:
```json
{
  "model": "claude-sonnet-4-5-20250929",
  "tools": [{"type": "web_search_20250305", "name": "web_search"}],
  "messages": [{"role": "user", "content": "Research [person] at [company] for a meeting about [topic]..."}]
}
```

## DATABASE
- briefs (id, user_id, meeting_title, meeting_date, attendees JSONB, company TEXT, brief_content JSONB, created_at)

## PRICING
- Free: 3 briefs/month
- Pro ($19/mo): Unlimited briefs, deep research, brief library, export

## DEPLOY: Vercel → meetbrief.buildkit.store

## BUILD ORDER
1. Setup + Auth + Supabase
2. Meeting input (paste invite parser + manual)
3. Claude API with web search for research
4. Brief generation and display
5. Brief library with search
6. PDF export
7. Quick prep mode
8. Stripe
9. Landing page + deploy
```

---

<a name="10-envguard"></a>
## 10. ENVGUARD — Environment Variable Manager

```
PROJECT: EnvGuard — Secure Env Var Manager for Teams
REPO: Create new repo `envguard` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

A secure place to store, share, and sync environment variables across projects and team members. Like 1Password for .env files. You currently have API keys scattered across Railway env vars, Vercel env vars, and local .env files with no central source of truth.

## TECH STACK
- Next.js 16 + TypeScript, Tailwind + shadcn/ui, Supabase, Vercel, Stripe
- Encryption: AES-256-GCM client-side encryption (keys never leave the browser in plaintext)

## FEATURES

1. **Projects**: Create projects (MoneyPrinter, PL Engine, PC Bottleneck, etc.). Each project has env vars.
2. **Environments**: Each project has environments: development, staging, production. Different values per environment.
3. **Var Management**: Add key-value pairs. Values encrypted at rest. Show/hide toggle (default: hidden). Copy individual value or entire .env file.
4. **Import/Export**: Paste a .env file → auto-parse into key-value pairs. Export as .env, .env.local, JSON, YAML, or Docker --env-file format.
5. **Sync**: CLI tool: `envguard pull --project moneyprinter --env production > .env` downloads the latest vars. `envguard push` uploads local .env to the project.
6. **Change Log**: Every change tracked: who changed what, when, old value (encrypted). Rollback to any previous version.
7. **Sharing**: Share a project with team members (email invite). Role-based: Admin (full access), Developer (read + use, can't delete), Viewer (read only).
8. **Security**: Client-side encryption (AES-256-GCM). Master password per user (never stored server-side). Values decrypted only in the browser. Audit log of all access.

## DATABASE
- projects (id, user_id, name, description, created_at)
- environments (id, project_id, name — 'development', 'staging', 'production')
- env_vars (id, environment_id, key TEXT, encrypted_value TEXT, version INT, updated_by UUID, updated_at)
- env_var_history (id, env_var_id, encrypted_value TEXT, version INT, changed_by UUID, changed_at)
- project_members (id, project_id, user_id, role TEXT)

## PRICING
- Free: 3 projects, 2 environments, 50 vars total, no sharing
- Pro ($12/mo): Unlimited projects, unlimited vars, all environments, team sharing, CLI, change log, audit

## DEPLOY: Vercel → envguard.buildkit.store

## BUILD ORDER
1. Setup + Auth + Supabase
2. Project + environment CRUD
3. Client-side encryption (AES-256-GCM with Web Crypto API)
4. Var management (add, edit, delete, show/hide)
5. Import/export (.env parser)
6. Change log + version history
7. Team sharing with roles
8. CLI tool (Node.js, published to npm)
9. Stripe
10. Landing page + deploy

## FIRST USE: Import your actual env vars from MoneyPrinter, PL Engine, Admin Dashboard, PC Bottleneck. One source of truth.
```

---

<a name="11-pixeldiff"></a>
## 11. PIXELDIFF — Visual Regression Testing

```
PROJECT: PixelDiff — Visual Regression Testing Tool
REPO: Create new repo `pixeldiff` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Take screenshots of your web pages before and after changes → highlight visual differences. Catch CSS bugs, layout shifts, and unintended changes before deploying. Integrates with CI/CD.

## TECH STACK
- Frontend: Next.js 16 + TypeScript, Tailwind + shadcn/ui, Vercel
- Backend: Python FastAPI + Playwright (screenshots), Railway
- Image Comparison: pixelmatch (Node.js) or Pillow (Python)
- Database: Supabase

## FEATURES

1. **Add Pages**: Add URLs to monitor. Group by project. Set viewport sizes (desktop 1440px, tablet 768px, mobile 375px).
2. **Baseline Capture**: Take initial screenshots of all pages → these are the "expected" state.
3. **Comparison Run**: Click "Run comparison" → takes new screenshots → overlays with baseline → generates diff image highlighting every pixel that changed. Red overlay on changed pixels. Side-by-side view: baseline | current | diff. Percentage changed per page.
4. **Auto-Run on Deploy**: Webhook endpoint. Add to your Vercel/Railway deploy webhook → auto-runs comparison after every deploy.
5. **Approve/Reject Changes**: If changes are intentional, click "Approve" → new screenshot becomes the baseline. If unintentional → flag as bug.
6. **History**: See all comparison runs over time. Track visual stability per page.
7. **Notifications**: Discord/email alert when visual changes detected > threshold (e.g., >2% pixels changed).

## DATABASE
- projects (id, user_id, name)
- pages (id, project_id, url, viewports TEXT[])
- baselines (id, page_id, viewport TEXT, screenshot_url TEXT, created_at)
- comparisons (id, page_id, viewport TEXT, baseline_screenshot_url TEXT, current_screenshot_url TEXT, diff_screenshot_url TEXT, diff_percentage FLOAT, status TEXT, run_id UUID, created_at)
- runs (id, project_id, trigger TEXT, status TEXT, started_at, completed_at)

## PRICING
- Free: 1 project, 5 pages, manual runs only
- Pro ($15/mo): Unlimited projects/pages, auto-run on deploy, notifications, history

## DEPLOY: Frontend → Vercel, Backend → Railway (needs Playwright)

## BUILD ORDER
1. FastAPI + Playwright screenshot service
2. Supabase + Next.js setup
3. Project + page management
4. Baseline capture
5. Comparison engine (pixelmatch or Pillow)
6. Diff display (side-by-side + overlay)
7. Approve/reject workflow
8. Webhook for auto-run on deploy
9. Notifications
10. Stripe + landing page + deploy
```

---

<a name="12-splitship"></a>
## 12. SPLITSHIP — Feature Flag Service

```
PROJECT: SplitShip — Simple Feature Flags
REPO: Create new repo `splitship` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Toggle features on/off in your apps without redeploying. Create a flag → wrap your code in an if statement → toggle from the dashboard. Like LaunchDarkly but for solo devs at 1/100th the price.

## TECH STACK
- Next.js 16 + TypeScript, Tailwind + shadcn/ui, Supabase (with Realtime for instant flag updates), Vercel, Stripe

## FEATURES

1. **Create Flags**: Name, key (auto-generated slug), description, default value (true/false). Environments: development, staging, production (different values per env).
2. **Dashboard Toggle**: Big on/off switches. See which flags are active in which environment at a glance. Change history: who toggled what, when.
3. **SDKs**: JavaScript SDK (npm package, <2KB): `import { SplitShip } from 'splitship'; const flags = await SplitShip.init('api-key'); if (flags.isEnabled('new-checkout')) { /* show new checkout */ }`. React hook: `const { isEnabled } = useSplitShip(); if (isEnabled('dark-mode')) {...}`. Python SDK: `from splitship import SplitShip; ss = SplitShip('api-key'); if ss.is_enabled('new-algo'): ...`. SDK polls API every 30s OR uses WebSocket for instant updates.
4. **Percentage Rollout**: Enable a flag for X% of users (gradual rollout). Based on user ID hash for consistency (same user always sees same version).
5. **Targeting Rules**: Enable flag for specific users (by ID or email). Enable for specific environments only.
6. **API**: REST API for all operations. `GET /api/flags?env=production` returns all flags for that env.
7. **Audit Log**: Every flag change logged: who, what, when, from what value to what value.

## DATABASE
- projects (id, user_id, name, api_key_hash TEXT)
- flags (id, project_id, name TEXT, key TEXT, description TEXT, created_at)
- flag_values (id, flag_id, environment TEXT, is_enabled BOOLEAN, rollout_percentage INT, targeting_rules JSONB, updated_by UUID, updated_at)
- flag_history (id, flag_id, environment TEXT, old_value BOOLEAN, new_value BOOLEAN, changed_by UUID, changed_at)

## PRICING
- Free: 1 project, 10 flags, 2 environments, API only
- Pro ($10/mo): Unlimited projects/flags, all environments, SDKs, percentage rollout, targeting, audit log

## DEPLOY: Vercel → splitship.buildkit.store

## BUILD ORDER
1. Setup + Auth + Supabase
2. Project + flag CRUD
3. Per-environment toggles
4. REST API endpoint
5. JavaScript SDK (npm package)
6. React hook wrapper
7. Python SDK
8. Percentage rollout
9. Targeting rules
10. Audit log
11. Stripe + landing page + deploy

## FIRST USE: Add feature flags to MoneyPrinter (toggle whale_only_mode, profit_ladder, fv_engine remotely without redeploying).
```

---

<a name="13-habitstack"></a>
## 13. HABITSTACK — Minimalist Habit Tracker

```
PROJECT: HabitStack — Minimalist Daily Habit Tracker
REPO: Create new repo `habitstack` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Track daily habits with a beautiful, minimal UI. GitHub-style contribution heatmap for each habit. No bloat — just habits, streaks, and accountability. Mobile-first PWA.

## TECH STACK
- Next.js 16 + TypeScript, Tailwind + shadcn/ui, Supabase, Vercel, Stripe
- PWA: next-pwa for installable mobile experience

## FEATURES

1. **Habits**: Create habits with: name, emoji icon, frequency (daily, weekdays only, custom days), goal (e.g., "3 times per week"). Reorder by drag-and-drop.
2. **Daily View**: Today's habits as a checklist. Tap to complete. Satisfying animation on completion (checkmark + streak counter). Shows current streak for each habit.
3. **Heatmap**: GitHub-style contribution grid for each habit (365-day view). Green = completed, gray = missed, white = not scheduled. Total completion percentage.
4. **Streaks**: Current streak, longest streak, total completions per habit. Streak freeze: 1 free "miss" per week without breaking streak (configurable).
5. **Stats**: Overall completion rate. Best habit (highest completion). Most improved. Calendar view showing all habits per day.
6. **Reminders**: Push notifications (PWA) at configured time. Optional: daily summary email.
7. **Social**: Public profile with your habit heatmaps (optional). Share a "challenge" link: invite someone to do the same habit and compare streaks.

## DATABASE
- habits (id, user_id, name, emoji, frequency JSONB, goal_per_week INT, display_order INT, is_active BOOLEAN, created_at)
- completions (id, habit_id, completed_date DATE, created_at)
- streaks (id, habit_id, current_streak INT, longest_streak INT, streak_freeze_used BOOLEAN, updated_at)
-- Unique constraint: one completion per habit per day
ALTER TABLE completions ADD CONSTRAINT unique_completion UNIQUE (habit_id, completed_date);

## PRICING
- Free: 5 habits, basic heatmap, no reminders
- Pro ($5/mo): Unlimited habits, reminders, streak freeze, stats, social profiles

## DESIGN
- Ultra-minimal. White space. Focus on the habits, not the UI.
- Light theme default (unusual for your products — this one should feel clean and calm)
- Heatmap: green gradient (#dcfce7 → #16a34a)
- Completion animation: confetti burst or satisfying check animation
- Mobile-first: designed for one-thumb operation

## DEPLOY: Vercel → habitstack.buildkit.store (PWA enabled)

## BUILD ORDER
1. Setup + Auth + Supabase + next-pwa config
2. Habit CRUD
3. Daily checklist view with completion toggle
4. Streak calculation logic
5. Heatmap component (365-day grid)
6. Stats page
7. Push notifications
8. Streak freeze feature
9. Public profiles
10. Stripe + landing page + deploy
```

---

<a name="14-slabot"></a>
## 14. SLABOT — SLA & Response Time Tracker

```
PROJECT: SLABot — Customer Response Time & SLA Tracker
REPO: Create new repo `slabot` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Track how fast you respond to customer emails/messages. Set SLA targets (e.g., "respond within 4 hours"). Dashboard shows: average response time, SLA compliance rate, who's waiting longest. For freelancers and small teams who promise fast response times.

## TECH STACK
- Next.js 16 + TypeScript, Tailwind + shadcn/ui, Supabase, Vercel, Stripe
- Integrations: Gmail API (future), manual for MVP

## FEATURES

1. **Tickets**: Manual entry for MVP: "Client: BarrelHouse, Subject: Phase 3 timeline question, Received: [timestamp]". Mark as "Responded" when you reply (records response time). Auto-calculate: time to first response.
2. **SLA Targets**: Set per-client or global: "Respond within 4 business hours". Dashboard shows: ✅ on track, ⚠️ approaching deadline, ❌ breached.
3. **Dashboard**: Average first response time (today, this week, this month). SLA compliance rate (% of tickets responded within target). Tickets by status (waiting, responded, resolved). Longest waiting ticket (highlighted prominently). Response time trend chart.
4. **Client Report**: Generate a monthly report per client showing: number of tickets, average response time, SLA compliance. Export as PDF. Send to client to prove your responsiveness.
5. **Business Hours Config**: Set your working hours (9am-6pm weekdays). SLA clock only ticks during business hours. Timezone-aware.
6. **Alerts**: Discord/email alert when a ticket is approaching SLA breach (e.g., at 75% of SLA window).

## DATABASE
- tickets (id, user_id, client_name TEXT, subject TEXT, channel TEXT, received_at TIMESTAMPTZ, responded_at TIMESTAMPTZ, resolved_at TIMESTAMPTZ, response_time_minutes INT, sla_target_minutes INT, sla_status TEXT, notes TEXT)
- sla_configs (id, user_id, client_name TEXT, target_minutes INT, business_hours JSONB)

## PRICING
- Free: 50 tickets/month, basic dashboard
- Pro ($10/mo): Unlimited tickets, client reports, alerts, PDF export, business hours config

## DEPLOY: Vercel → slabot.buildkit.store

## BUILD ORDER
1. Setup + Auth + Supabase
2. Ticket CRUD (manual entry)
3. SLA target configuration
4. Dashboard with response time metrics
5. SLA status calculation (respecting business hours)
6. Alerts (Discord + email)
7. Client report generation (PDF)
8. Response time trend charts
9. Stripe
10. Landing page + deploy
```

---

<a name="15-pagespeed"></a>
## 15. PAGESPEED — Website Performance Audit Tool

```
PROJECT: PageSpeed — Website Performance Audit & Monitoring
REPO: Create new repo `pagespeed` in C:\Users\Kruz\Desktop\Projects\

## WHAT TO BUILD

Enter a URL → get a full performance audit: load time, Core Web Vitals, image optimization, bundle size, SEO score. Schedule recurring audits. Track performance over time. Like Lighthouse but with history, monitoring, and alerts.

## TECH STACK
- Frontend: Next.js 16 + TypeScript, Tailwind + shadcn/ui, Vercel
- Backend: Python FastAPI, Railway
- Audit Engine: Google PageSpeed Insights API (free, 25K queries/day) + Lighthouse CI
- Database: Supabase

## FEATURES

1. **One-Time Audit**: Enter URL → run PageSpeed Insights API → display results:
   - Performance score (0-100)
   - Core Web Vitals: LCP (Largest Contentful Paint), FID/INP (Interaction to Next Paint), CLS (Cumulative Layout Shift)
   - Time to First Byte, First Contentful Paint, Speed Index, Time to Interactive
   - Opportunities: image optimization, unused JavaScript, render-blocking resources
   - Diagnostics: DOM size, main thread work, cache policy
   - SEO checks: meta tags, headings, alt text, mobile-friendly
   - Accessibility score
2. **Scheduled Monitoring**: Add URLs to monitor. Check daily or weekly. Track all metrics over time. Charts showing: performance score trend, LCP trend, CLS trend.
3. **Comparison**: Compare two URLs side-by-side. Compare mobile vs desktop. Compare current vs previous audit.
4. **Alerts**: Email/Discord alert when performance drops below threshold. Alert when Core Web Vitals fail (red zone).
5. **Reports**: PDF report per URL with: current scores, trend over time, top 5 recommendations. White-label option for agencies (Pro tier).
6. **Competitor Tracking**: Add competitor URLs → compare your performance against theirs.

## API INTEGRATION
```python
# Google PageSpeed Insights API (free)
import httpx

async def audit_url(url: str, strategy: str = "mobile") -> dict:
    api_url = "https://www.googleapis.com/pagespeedonline/v5/runPagespeed"
    params = {
        "url": url,
        "strategy": strategy,  # "mobile" or "desktop"
        "category": ["performance", "seo", "accessibility", "best-practices"],
        "key": GOOGLE_API_KEY
    }
    async with httpx.AsyncClient(timeout=60) as client:
        resp = await client.get(api_url, params=params)
        return resp.json()
```

## DATABASE
- sites (id, user_id, url TEXT, name TEXT, check_frequency TEXT, last_checked_at, created_at)
- audits (id, site_id, strategy TEXT, performance_score INT, seo_score INT, accessibility_score INT, lcp_ms INT, inp_ms INT, cls FLOAT, ttfb_ms INT, fcp_ms INT, speed_index INT, tti_ms INT, opportunities JSONB, diagnostics JSONB, created_at)
- alert_configs (id, user_id, site_id, metric TEXT, threshold INT, channel TEXT)

## PRICING
- Free: 5 audits/day, no monitoring, no history
- Pro ($15/mo): Unlimited audits, daily/weekly monitoring, alerts, history, PDF reports, competitor tracking
- Agency ($39/mo): White-label reports, team access, 50 monitored URLs

## DEPLOY: Frontend → Vercel, Backend → Railway

## BUILD ORDER
1. FastAPI + PageSpeed Insights API integration
2. Next.js setup + Supabase
3. One-time audit: enter URL → display results
4. Score display with gauges (performance, SEO, accessibility)
5. Core Web Vitals cards with status indicators
6. Opportunities and diagnostics lists
7. Scheduled monitoring (cron on Railway)
8. Performance trend charts (Recharts)
9. Comparison view (side-by-side)
10. Alerts (Discord + email)
11. PDF report generation
12. Competitor tracking
13. Stripe + landing page + deploy

## FIRST USE: Audit all your deployed products. Find out which ones are slow. Fix them.
```

---

## THE ECOSYSTEM MAP — ALL 37 PROMPTS

Here's how all 37 projects across 3 batches connect:

### Batch 1 (10 projects): Foundation + Fixes
ReadMeNow, QuestForge, ReplyBot, Archive Repos, AI Finance Brief Email, BarrelHouse Phase 3, PL Engine Hardening, PyInstaller EXE, MoneyPrinter Dashboard v2, buildkit.store/tools

### Batch 2 (12 projects): SaaS Products + Infrastructure
ProofBlock, PulseCheck, ShipFolio, OnePost, InvoiceForge, WaitlistKit, ChangelogHQ, ScreenshotAPI, CronPilot, PolicyDraft, FeedbackLoop, StatusBrew

### Batch 3 (15 projects): Developer Tools + Business Tools
PitchCraft, DealFlow, SnipVault, OGImage, FormForge, DockLog, LinkHive, ClipDeck, MeetBrief, EnvGuard, PixelDiff, SplitShip, HabitStack, SLABot, PageSpeed

### Synergy Map
```
YOUR BUSINESS OPERATIONS:
  PitchCraft (write proposals) → DealFlow (track deals) → InvoiceForge (send invoices) → SLABot (track response times)

YOUR DEVELOPMENT WORKFLOW:
  EnvGuard (manage secrets) → SplitShip (feature flags) → PixelDiff (visual testing) → PageSpeed (performance) → PulseCheck (uptime) → CronPilot (cron jobs) → DockLog (logs) → StatusBrew (status page)

YOUR MARKETING:
  WaitlistKit (pre-launch) → OGImage (social cards) → OnePost (content repurpose) → ProofBlock (testimonials) → ChangelogHQ (updates) → FeedbackLoop (user feedback)

YOUR PRODUCTIVITY:
  SnipVault (code snippets) → LinkHive (bookmarks) → ClipDeck (clipboard) → MeetBrief (meeting prep) → HabitStack (daily habits)

YOUR PRODUCTS:
  ReadMeNow (docs) → ShipFolio (portfolio) → buildkit.store/tools (SEO traffic) → FormForge (lead capture) → PolicyDraft (legal) → ScreenshotAPI (screenshots)
```

---

## BATCH 3 PRIORITY ORDER

| # | Project | Type | Sessions | Why |
|---|---------|------|----------|-----|
| 1 | **PitchCraft** | Business | 2-3 | You have uninvoiced client work RIGHT NOW |
| 2 | **DealFlow** | Business | 1-2 | Track your actual pipeline |
| 3 | **EnvGuard** | Dev Tool | 1-2 | You need this, every dev needs this |
| 4 | **OGImage** | Infrastructure | 1 | Every product needs OG images, tiny scope |
| 5 | **SplitShip** | Dev Tool | 2 | Feature flags for MoneyPrinter, reusable everywhere |
| 6 | **FormForge** | SaaS | 2-3 | Big market, AI differentiator |
| 7 | **PageSpeed** | Tool | 1-2 | Free Google API, useful for your own sites |
| 8 | **SnipVault** | Productivity | 1-2 | Devs love snippet managers, simple MVP |
| 9 | **DockLog** | Dev Tool | 2 | Solves your multi-service log problem |
| 10 | **MeetBrief** | AI Tool | 1-2 | Impressive demo, uses Claude web search API |
| 11 | **PixelDiff** | Testing | 2 | Visual regression catches CSS bugs |
| 12 | **LinkHive** | Productivity | 1-2 | Chrome extension = distribution channel |
| 13 | **HabitStack** | Consumer | 1 | Fun, small, good portfolio piece |
| 14 | **ClipDeck** | Productivity | 1-2 | Niche but sticky |
| 15 | **SLABot** | Business | 1 | Simple, useful for client work |

---

*Batch 3 of 3. 37 total pipeline-ready prompts across all batches. Copy. Paste. Ship.*
