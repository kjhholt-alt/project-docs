# Claude Design — Specs (2026-04-17)

> **Round 1 (Specs 1–3):** shipped 2026-04-17. Spec 1 live at
> [/for/wright-business-advisors](https://deal-brain.vercel.app/for/wright-business-advisors);
> Spec 3 at [ai-ops-consulting/outreach/templates/new/](../ai-ops-consulting/outreach/templates/new/).
> Spec 2 deferred.
>
> **Round 2 (Specs 4–6):** added 2026-04-17 after Wayne's Spec 1 feedback.
> These are follow-ups that make the shipped designs pay off: a polished
> paid-report template, the $3,500 Sprint Kit deliverable, and the main
> landing page that every future responder arrives at first.

# Round 1

Claude Design launched today at [claude.ai/design](https://claude.ai/design).
Free with your Pro/Max/Team plan. Exports: Canva, PDF, PPTX, HTML, folders.
Hand-off to Claude Code is a first-class feature.

This doc contains three specs, ranked by ROI. For each one:

1. **Context** — what exists now, what this will replace / add.
2. **Paste into claude.ai/design** — the exact prompt to use. Just paste it verbatim.
3. **Iterate checklist** — what to look for in Claude Design's output before accepting.
4. **Hand back to me** — how to deliver the result so I can wire it into the codebase.

Work through them **in order**. #1 is the highest leverage because it feeds
the hot-lead-triage pipeline we just shipped — every personalized page we
generate for new responders will use whatever you design here.

---

## SPEC 1 — Broker-Handoff Landing Template (DealBrain)

**HIGHEST ROI.** This becomes the new base template for
[build_custom_landing.py](../deal-brain/scripts/build_custom_landing.py).
Every future personalized page (like the one we built for Wayne) will
look like what you design here.

### Context

- **Product:** DealBrain — 60-second AI due diligence for business buyers
- **Audience:** Mid-sized business brokers + buyers (Wayne-profile). Senior
  M&A professionals. 20+ years experience. Read dense reports, print them,
  take them to meetings. Do NOT want SaaS-bro aesthetics.
- **Current page:** [live reference](https://deal-brain.vercel.app/for/wright-business-advisors) —
  functional but generic-DealBrain (zinc + emerald dark theme, Lucide icons).
  Gets the job done; doesn't stand out.
- **What we want instead:** Editorial-grade investment memo feel. Think
  Stratechery × Bain case study × a premium Figma report. Dense, serious,
  designed to be read on a 27" monitor AND printed on US Letter paper.
- **Must work:** This is a Next.js 16 App Router page. Tailwind CSS only
  (no shadcn, no Framer Motion, no extra libs). Lucide icons are allowed.
  Dark theme preferred but light-mode-printable is a bonus.

### Paste into claude.ai/design

```
Design a single-page React (Next.js + Tailwind CSS) landing/report page
for DealBrain, an AI-powered due diligence tool for small-to-mid market
business acquisitions ($1M-$25M revenue range).

The audience is senior business brokers and buyers — people with
Master-level M&A certifications, 20+ years in the industry. They print
reports, take them to meetings, and skim-read them on large monitors.
Avoid SaaS-bro / startup aesthetics entirely. Target visual language:
editorial-grade investment banking memo meets modern Figma portfolio.

Fonts: prefer a serif for headlines (Fraunces, EB Garamond, or similar)
and a clean sans-serif for data (Inter, IBM Plex Sans, or similar).
Data should feel primary; whitespace is a tool, not a goal.

Visual tone: premium, dense, confident. Palette: dark charcoal
background (#0a0a0f range) with a sparingly-used single accent color
(I suggest a warm gold or burnt orange instead of the generic emerald
green I'm using today). Risk tags can use red/amber/emerald for
severity but the rest should feel restrained.

The page has these sections in this order, and each section needs its
own distinct visual treatment so a reader can navigate by scanning:

1. PERSONAL BANNER AT TOP — small, one-paragraph, addressed to the
   specific prospect ("Hey Wayne, I rebuilt the sample as a lower-
   middle-market manufacturing deal..."). Includes the firm name they
   represent. This is a greeting, not a CTA.

2. REPORT HEADER — big deal name (serif, tracking-tight), location
   subline, then a 4-up metadata grid (Report ID / Date / Asking Price
   / EBITDA Multiple). To the right of the grid, a prominent Deal
   Score gauge (0-100 semicircle) with a one-line caption under it
   ("Workable at renegotiated terms" / "Walk" / "Strong buy"). Below
   the grid, pills showing red-flag count, warning count, info count,
   and documents-analyzed count.

3. EXECUTIVE SUMMARY — 4 colored bullets (red/amber/emerald) followed
   by a highlighted "Analyst Recommendation" callout with a single-
   word verdict (BUY / NEGOTIATE / WALK) and 2-3 sentence rationale.

4. FINANCIAL ANALYSIS — a 3-5 year Revenue + EBITDA + margin table
   with YoY columns, then side-by-side: (a) Margin Analysis bars
   showing the deal's margins vs industry average, (b) EBITDA
   Normalization waterfall showing reported → adjusted → risk-adjusted.

5. RISK MATRIX — 6-10 rows. Each row: category name, severity badge
   (LOW/MEDIUM/HIGH/CRITICAL with color treatment), one-line detail.
   This table is the most-reviewed piece of the whole report by
   experienced brokers — give it real design attention.

6. DETAILED FINDINGS BY AGENT — three groups (Financial, Contract,
   Market). Each group has 2-4 findings. Each finding is a collapsible
   card with severity badge, title, detail paragraph, and a
   "Recommendation" sub-block.

7. CTA SECTION — personalized to the firm ("Want this on a real Wright
   Business Advisors deal?"). Small and restrained; no big orange
   buttons.

8. FOOTER DISCLAIMER — legal boilerplate, muted.

Hard constraints:
- Must be a single React functional component that accepts a
  `DealReport` prop with this TypeScript shape (do NOT inline the
  sample data; it will be injected by a generator script):

    interface DealReport {
      id: string;
      dealName: string;
      dealTagline: string;
      dealLocation: string;
      prospectFirm: string;
      prospectFirstName: string;
      industryDisplay: string;
      industryKeywords: string;
      reportId: string;
      reportDate: string;
      askingPrice: string;
      multipleLabel: string;
      multiple: string;
      dealScore: number;         // 0-100
      scoreCaption: string;
      riskScore: {
        overall: number;          // 1-10 (10 = walk)
        financial: number;
        contract: number;
        market: number;
      };
      executiveSummary: string;
      bullets: { color: "red"|"amber"|"emerald"; text: string }[];
      analystVerdict: "BUY" | "NEGOTIATE" | "WALK";
      analystRecommendation: string;
      revenueTable: {
        year: string; revenue: string; ebitda: string;
        margin: string; yoy: string;
      }[];
      margins: {
        label: string; value: string; industryAvg: string;
        bar: number;  // 0-100
      }[];
      ebitdaNormalization: {
        label: string; value: string;
        sign: "" | "add" | "deduct" | "sub-total" | "total";
      }[];
      riskMatrix: {
        category: string;
        level: "LOW"|"MEDIUM"|"HIGH"|"CRITICAL";
        detail: string;
      }[];
      findings: {
        id: string;
        agent: "financial"|"contract"|"market";
        severity: "red-flag"|"warning"|"info";
        title: string;
        detail: string;
        recommendation: string;
      }[];
      documents: { name: string; docType: string }[];
    }

- Use only Tailwind CSS utility classes (no extra libraries, no CSS
  files). Lucide React icons are OK.
- Print-friendly: when printed, the layout should still read top-to-
  bottom without clipping. (Use @media print minimally if needed via
  Tailwind's `print:` variants.)
- No animations that aren't instant. No scroll-linked parallax.
- Mobile should degrade gracefully (single column), but desktop is
  the primary target.

Render the page against this example DealReport prop so I can see it
populated realistically:

    dealName: "Summit Precision Machining, Inc."
    dealTagline: "Aerospace CNC Machining (AS9100D, ITAR)"
    dealLocation: "Longmont, CO"
    prospectFirm: "Wright Business Advisors"
    prospectFirstName: "Wayne"
    industryDisplay: "manufacturing"
    askingPrice: "$6,200,000"
    multipleLabel: "EBITDA Multiple"
    multiple: "4.0× (adj. 4.4×)"
    dealScore: 58
    scoreCaption: "Workable at renegotiated terms"
    riskScore: { overall: 5, financial: 4, contract: 7, market: 4 }
    analystVerdict: "NEGOTIATE"
    (fill the other fields with plausible aerospace-CNC data —
    customer concentration, aging equipment, key-programmer risk)

Iterate until the design feels like something a boutique M&A firm
would be proud to show a buyer.
```

### Iterate checklist (what to look for in Claude Design before accepting)

- [ ] Typography: serif headline for deal name. Numbers in a mono or
      tabular-figure sans. Body in a readable sans.
- [ ] Deal Score gauge is visually prominent, not buried.
- [ ] Risk matrix is scannable — severity badges should be the eye's
      first anchor per row.
- [ ] All 4 sections are visually distinct (different card treatments,
      separators, or background tints) so skim-reading works.
- [ ] Print preview: Ctrl+P the Claude Design preview — does it
      actually look like a decent printout? Page breaks in sane places?
- [ ] Mobile: collapse cleanly to single column.
- [ ] Zero stock-photo energy.
- [ ] Accent color is NOT emerald green. Gold/burnt-orange/aubergine —
      something that feels broker-premium.

### Hand back to me

In Claude Design, use the **"Handoff to Claude Code"** feature (or export
the HTML/React). Then tell me in chat:

> "Claude Design output is ready for Spec 1. Here is the React code: [paste]"

Or if it's a long paste:

> "Claude Design output saved to `C:/Users/Kruz/Desktop/Projects/deal-brain/design/broker-handoff-v1.tsx`"

I'll then:

1. Adapt the generated component to accept the `DealReport` interface
   (most likely Claude Design will hardcode the sample — I'll swap that for
   props).
2. Replace the `TEMPLATE_TSX` constant in [build_custom_landing.py](../deal-brain/scripts/build_custom_landing.py:246)
   with the new design.
3. Regenerate Wayne's page with `py scripts/build_custom_landing.py
   --config dossiers/landing_pages/test-wright.yaml --no-deploy` and
   screenshot it for you to approve.
4. Only then deploy to production.

---

## SPEC 2 — PL Engine Factory One-Pager

**MEDIUM-HIGH ROI.** Adds a one-page executive summary to the PL Engine's
existing 19-slide OH Review deck. Factory leadership often skims; a
1-pager becomes their default touchpoint.

### Context

- **Product:** PL Engine — Python pipeline that replaces a 2-3 day
  manual Excel overhead-budgeting process at 6 John Deere manufacturing
  factories.
- **Existing deliverable:** 19-slide PPTX deck per factory, generated by
  [pptx_builder.py](../pl-engine/src/pptx_builder.py) using python-pptx.
  Dense, detail-rich. Good for deep reviews; too much for a 5-minute
  exec briefing.
- **What we want to add:** A ONE SLIDE US-Letter-portrait template
  that summarizes a factory's overhead budget cycle at a glance. Stays
  on the wall, prints cleanly, readable from 6 feet away.
- **Constraints:** Must be conservative visually — this goes to John
  Deere factory leadership; no flashy design. Must NOT imitate official
  JD branding (we're not authorized). Legible in B&W printouts.

### Paste into claude.ai/design

```
Design a ONE-PAGE executive summary template for a factory overhead
budget review. The output will be a .pptx file, US Letter portrait
(8.5in × 11in), one slide.

Audience: John Deere manufacturing factory leadership (plant managers,
finance directors, factory controllers). They are senior, conservative,
data-literate. They will print this on B&W laser printers and pin it
to a board or drop it in a review meeting.

Visual style: conservative corporate manufacturing, NOT startup/SaaS.
Palette: navy (#1e3a5f), steel gray (#4a5568), warm white background
(#fafaf7). Exactly ONE accent color for warnings/flags — amber
(#d97706). Nothing else colored; all other text is navy or gray.

Typography: a serious sans-serif. Calibri, Aptos, or similar
corporate-standard font that renders identically on Windows and Mac.
Tabular figures for all numbers. NO decorative fonts.

CRITICAL: must not pretend to be official John Deere material. Use no
JD green, no JD logos or deer silhouettes, no "John Deere" wordmark.
The factory codes (AX02, JL01, etc.) are fine; they're internal
engineering codes.

Layout — this is the structure, not a suggestion:

    ┌───────────────────────────────────────────────────────┐
    │  FACTORY HEADER                                       │
    │  ─ Factory name (large) + code (small)                │
    │  ─ Cycle label (e.g. "PL2027 Overhead Review")        │
    │  ─ Analyst name + date                                │
    ├──────────────────────────┬────────────────────────────┤
    │  KEY NUMBERS (4-up grid) │  DEAL SCORE / VARIANCE     │
    │  ─ Normal Hours          │  BIG CIRCLE SHOWING        │
    │  ─ D+P Normal            │  variance % vs prior cycle │
    │  ─ CPOH at Normal        │  (+/-, with color: amber   │
    │  ─ Headcount total       │  if > threshold)           │
    ├──────────────────────────┴────────────────────────────┤
    │  TOP 5 COST CATEGORIES (horizontal stacked bar chart) │
    │  showing $M per scenario (Threshold / Normal / Plus)  │
    ├───────────────────────────────────────────────────────┤
    │  TOP 5 CC DRIVERS OF VARIANCE (horizontal bar)        │
    │  CC code | description | $ change vs prior            │
    ├──────────────────────────┬────────────────────────────┤
    │  HEADCOUNT TABLE         │  KEY FLAGS / CALLOUTS      │
    │  Wage | Salary | Contract │  3-5 bullets with amber    │
    │  prior vs current        │  dot for warnings           │
    ├──────────────────────────┴────────────────────────────┤
    │  ANALYST NARRATIVE (2-3 sentences, italic)            │
    ├───────────────────────────────────────────────────────┤
    │  FOOTER: small disclaimer + source + page 1 of 1      │
    └───────────────────────────────────────────────────────┘

Everything should fit on ONE US Letter page without scrolling or
clipping. Assume 0.5-inch margins all around.

Deliverable: a .pptx file with NAMED shape placeholders so a Python
script (python-pptx) can find and replace the dynamic content. Use
placeholder text like {FACTORY_NAME}, {CYCLE_LABEL}, {DP_NORMAL}, etc.
For shapes that hold the charts, make them rectangles that a script
can replace with a generated chart image.

Render it populated with realistic sample data for a factory called
"Valley City" (code AX02): Normal Hours 171,684; D+P Normal $43.8M;
CPOH $255; Headcount 203. Show a +5.2% variance vs prior cycle in the
variance circle. Make up plausible Top-5 cost categories (Wages,
Benefits, Purchased Services, Depreciation, Supplies) and Top-5 CC
drivers.
```

### Iterate checklist

- [ ] Prints cleanly in B&W — no info is carried only by color.
- [ ] All numbers are visible at a glance; no eye-hunting.
- [ ] Variance circle jumps out FIRST when you look at the page.
- [ ] No JD branding accidentally copied.
- [ ] All placeholder text uses `{ALL_CAPS_UNDERSCORE}` format.
- [ ] Fonts are system-safe (Calibri/Aptos/Arial — NOT Google Fonts).
- [ ] Fits on exactly one page with 0.5" margins.

### Hand back to me

Export as **PPTX**. Save to
`C:/Users/Kruz/Desktop/Projects/pl-engine/templates/one_pager_template.pptx`
and tell me:

> "Spec 2 template saved to pl-engine/templates/one_pager_template.pptx"

I'll then:

1. Add a `build_one_pager(factory_code)` function in
   [pptx_builder.py](../pl-engine/src/pptx_builder.py).
2. It'll open the template, iterate named shapes, populate them from the
   existing factory data pipeline (same source as the 19-slide deck).
3. Add a `--one-pager` flag to [run.py](../pl-engine/run.py) to generate
   just the one-pager (without the full deck) for faster iteration.
4. Add a pytest that verifies the template loads and populates.
5. Run it against AX02 and send you the output for approval.

---

## SPEC 3 — Branded HTML Cold-Outreach Emails

**MEDIUM ROI.** Existing templates in
[outreach/templates/](../ai-ops-consulting/outreach/templates/) are HTML
but very plain (Gmail-draft-quality). Branded versions should lift
open-reply rate modestly.

### Context

- **Existing templates:** `smb.py`, `agency.py`, `broker.py`, `legal.py`,
  `restaurant.py` — each has a 3-touch sequence (Day 0, Day 3, Day 7).
  All use plain inline-styled HTML.
- **Three products to cover:**
  - **AI Ops in a Week** — $3,500 flat automation audit for SMBs
  - **DealBrain** — 60-second AI due diligence for business buyers/brokers
  - **Prospector Pro** — niche lead-gen for local service businesses
- **Email client reality:** Gmail, Outlook, Apple Mail dominate. Dark-mode
  support is uneven. Inline CSS only (no `<style>` block guaranteed).
  Avoid background images, custom fonts, JS. Table-based layout for
  Outlook compatibility.
- **Anti-spam:** no images > 200KB, plenty of text, one CTA max per
  email, NOT all-caps anywhere, no "FREE" or "!!!" triggers.

### Paste into claude.ai/design

```
Design a branded HTML email template system for cold outreach, used by
a solo founder reaching small business owners and service-firm owners
(brokers, agency owners, restaurant owners, attorneys). Three separate
product brands need templates:

1. "AI Ops in a Week" — a $3,500 flat-rate week-long automation
   engagement for SMBs. Tone: pragmatic, no-BS, results-first.
   Color: deep teal (#0f766e) as single accent.

2. "DealBrain" — AI-powered due diligence reports for business buyers
   and brokers. Tone: serious, analyst-grade, confidence-led. Color:
   warm gold (#b45309) as single accent.

3. "Prospector Pro" — niche lead generation for local service
   businesses (pool companies, landscapers, HVAC, etc.). Tone:
   practical, buyer-hungry, time-saving. Color: deep purple
   (#6d28d9) as single accent.

Each product needs TWO templates: a Touch-1 (cold first-contact) and
a Touch-2 (3-day follow-up, shorter, re-raises value prop differently).

Hard email constraints:
- Inline CSS only. No <style> blocks.
- TABLE-based layout for Outlook compatibility.
- Max width 560px.
- Font stack: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
  sans-serif. No custom / Google fonts.
- No images larger than 200KB. Preferably zero images — just text and
  CSS-rendered visuals (colored tables, dividers).
- ONE call-to-action per email. Subtle but clear button.
- Dark-mode safe: use colors that read OK on both light and dark
  backgrounds.
- Plain-text fallback for each (for clients that strip HTML).

Required merge-variable slots in each template, exactly these
curly-brace names: {first_name}, {business_name}, {city}, {industry},
{sender_first}, {sender_product}, {sender_url}, {unsubscribe_url}.

Layout for each Touch-1 email:
- Small product wordmark at top (text only, colored with the product
  accent color — no image).
- Headline (2-line max), plain text, slightly larger than body.
- 1 personalization line acknowledging their business by name.
- 3 bullet points of value (use simple table rows or CSS bullets).
- ONE call-to-action button/link.
- Short sign-off (first name + product name).
- Unsubscribe link in footer (small, gray, but legible).

Layout for each Touch-2 email:
- No wordmark (assume they recognize the thread).
- Subject prefixed "Re:" signals follow-up — keep very short body.
- 2-3 sentence message pivoting to a different angle than Touch-1.
- ONE call-to-action link, inline (not button).
- Same sign-off and unsubscribe footer.

Deliverable: 6 HTML snippets (3 products × 2 touches), each as a
complete self-contained <div>...</div> snippet (no full <html> doc —
this is what the Python sender will paste into Gmail's compose).
Include a matching plain-text fallback for each.

Use realistic sample content for each product — assume I haven't
told you what to say. You know what sells an automation audit, a DD
tool, and a lead-gen product to these audiences. Write confidently.
```

### Iterate checklist

- [ ] Each email renders OK in Gmail (test in Claude Design's preview).
- [ ] Dark mode: text is still readable.
- [ ] Mobile width (360px): no horizontal scroll.
- [ ] CTA is obvious but not aggressive.
- [ ] NO all-caps. NO exclamation points. NO "FREE" in subject.
- [ ] Merge variables present and spelled exactly right.
- [ ] Tone matches the product brand descriptor (pragmatic / serious /
      practical).

### Hand back to me

In Claude Design, export as **HTML folder** or paste each of the 6
snippets back to me. Save them to
`C:/Users/Kruz/Desktop/Projects/ai-ops-consulting/outreach/templates/new/`
(as separate `.html` files named `ai_ops_touch1.html`, etc.) and tell me:

> "Spec 3 HTML snippets saved under outreach/templates/new/"

I'll then:

1. Adapt each snippet into a Python dict matching the existing template
   shape (`SEQUENCE = [{"subject": ..., "html": ...}, ...]`).
2. Write the plain-text fallback counterparts.
3. A/B-flag the new templates behind `USE_NEW_TEMPLATES=1` env so we
   can compare reply rates before fully switching.
4. Run a preview-render on 3 synthetic leads (no send) and show you
   the rendered result in a browser window.

---

## How to run these in parallel (or in order)

- **Sequential:** Spec 1 first (feeds the hot-lead pipeline immediately),
  then #2, then #3. Lowest cognitive load.
- **Parallel:** Claude Design sessions are cheap on your plan. Open three
  tabs, paste Spec 1 / 2 / 3 into each, and let all three design in
  parallel. Come back in a few hours and pick the best of each.

## What to tell me when you start

Just say:

> "Started Spec N in Claude Design."

I'll stay out of your way while you iterate. Ping me when you have
output and I'll do the rest.

## Why these three and not others?

Considered and deferred:

- **Redesigning the DealBrain main landing page.** It's actively
  converting (Wayne replied on it today). Don't touch a working funnel
  until we have signal from ≥5 beta users.
- **Redesigning the PL Engine 19-slide deck.** Load-bearing for your day
  job; full redesign is a bigger project than it looks. Ship the
  one-pager add-on first.
- **Designing a DealBrain "full report" PDF export.** Could be great,
  but we don't have PDF export wired yet — premature.

---

# Round 2 (added 2026-04-17 after Wayne's Spec 1 feedback)

These three specs make the Round-1 shipped work actually pay off in
revenue. Do them in this order — #4 is the highest-leverage design you
can do right now because it's the actual paid product experience.

---

## SPEC 4 — DealBrain Paid Report (`/report/[id]`)

**HIGHEST ROI NOW.** This is the $199 deliverable. When a buyer uploads
their docs and gets back an analysis, *this* is the page they look at.
Today it's 468 lines of the old zinc/emerald SaaS aesthetic — the exact
look Wayne bounced off of in cold outreach. A prospect can't see a
premium editorial memo in the email and then open a generic SaaS report
after paying $199. That's a bait-and-switch we don't want to run on
our first real customers.

### Context

- **Route:** `/report/[id]` — dynamic, fetches report by ID from Supabase
- **Shape:** consumes the same `DealReport` TypeScript interface as
  `/for/wright-business-advisors` (already used there)
- **Client:** purchaser — a real buyer or broker who paid for this.
  Treat them as a customer, not a lead.
- **Differences from the editorial /for/ page:**
  1. **No personal banner.** No "Hey Wayne". Replace with a client
     attestation block: buyer name, buyer firm, report prepared for X
     on date Y by DealBrain.
  2. **Download/Share/Email affordances** — prominent but restrained.
     A paid report needs to be shareable with their lender, co-investor,
     attorney.
  3. **Print layout is first-class.** Customers WILL print this and
     take it to meetings. A4 + US Letter page breaks, no orphaned
     content.
  4. **Attestation footer.** This is a paid deliverable — it should
     end with a signed/dated block that looks like a professional
     work product, not a SaaS app screen.
- **Must reuse:** the editorial design system shipped for Wayne. Same
  fonts (Fraunces + Inter + JetBrains Mono), same palette, same
  §-numbering. This is a VARIANT of the same design system, not a new
  one.
- **Live reference:** <https://deal-brain.vercel.app/for/wright-business-advisors>
- **Current old implementation (to replace):**
  [src/app/report/[id]/page.tsx](../deal-brain/src/app/report/%5Bid%5D/page.tsx)

### Paste into claude.ai/design

```
Design the paid-customer version of the DealBrain due-diligence report
page. This is the $199 deliverable — when a buyer uploads their deal
documents, this is what they get back. Production React page, Next.js
App Router, Tailwind CSS.

Inherit the visual system from the editorial memo version I already
shipped (charcoal #0b0b10 background, burnt-gold accent, Fraunces
serif + Inter data + JetBrains Mono for IDs, §-numbered sections,
hairline dividers, no SaaS-bro gradients or emerald). Match the
typographic hierarchy exactly — this should feel like the same product,
not a sibling.

KEY DIFFERENCES from the editorial memo version:

1. NO personal banner at the top. Instead:
   - Client attestation block (top-right): "Prepared for: {buyer_name},
     {buyer_firm}" with date and a short confidentiality notice.
   - Report metadata strip (top-left): Report ID (mono), Revision,
     Generated At (UTC), Engine version, Document Count ingested.

2. UTILITY BAR at the very top of the report (above the header
   attestation): compact row with three primary actions, no-
   nonsense icons-plus-labels:
   - Download PDF
   - Share link (copies a signed share URL to clipboard)
   - Email to co-investor/lender (opens mailto with the share URL
     pre-filled).
   These are design-level affordances — just stubs / non-functional
   buttons. I'll wire them up later. Keep them RESTRAINED — gold text
   on charcoal, hairline border, NO big SaaS CTA buttons.

3. CONTENT SECTIONS, same §01–§06 order as the editorial memo:
   §01 Executive Summary  (same 4-bullet + analyst-recommendation callout)
   §02 Financial Analysis (3-yr trend + addback bridge)
   §03 Risk Matrix        (findings list + heatmap)
   §04 Documents Analyzed (ingested + coverage + gaps)
   §05 Valuation          (comps + recommended range)
   §06 Agent Consensus    (3-agent narrative)
   All sections use the same components as the editorial version.

4. ATTESTATION FOOTER — NEW section at the bottom. This replaces the
   sales-y "Want this on a real deal?" CTA from the editorial memo.
   Should look like the signature block of a formal memo:
   - "Prepared by" with a gold script-style signature of "Kruz Holt"
   - Title: "Founder, DealBrain"
   - Date (ISO)
   - Report ID (mono)
   - Engine + checksum + token count
   - A muted disclaimer about human review required before LOI,
     and that the report is AI-assisted analysis not professional
     advice.
   Tone: this is a professional work product. Match the gravity of
   an M&A pitchbook closing page.

5. PRINT LAYOUT IS FIRST-CLASS. Use Tailwind `print:` variants where
   needed. Specifically:
   - Page breaks BEFORE each §-numbered section so printouts split
     cleanly.
   - Hide the utility bar when printing.
   - Attestation block repeats at bottom of last printed page.
   - Swap charcoal background → white; paper-cream text → near-black
     on light. Keep serif headline + tabular numbers.
   - Margins sized for US Letter AND A4.

6. PREPARED-FOR block at the top-right of the report header should
   look like an engraved card — thin gold border, tight serif
   italic, dateline. This is the ONE spot on the page that
   explicitly addresses the purchaser by name. It's not a CTA or a
   greeting — it's formal attribution.

Hard constraints:
- Reuse the existing DealReport TypeScript interface with ONE
  addition — a new optional `purchaser` block:
    purchaser?: {
      name: string;
      firm?: string;
      email: string;
      purchasedAt: string;   // ISO
    }
  If `purchaser` is absent, render a "DEMO / NOT FOR DISTRIBUTION"
  watermark across the header area instead of the attestation.
- Tailwind CSS only, Lucide React icons OK, no new libraries.
- NO animations. This is a document, not a web app.
- NO sales / conversion affordances. No "buy another", no "upgrade",
  no pricing callouts. This is a CLOSED deliverable — they already
  paid. The only outbound links are the attestation email address
  and the unsubscribe/support link in the footer.

Render it populated against the same Summit Precision Machining
example data used for the editorial memo, with purchaser:
    name: "Marcus Chen"
    firm: "Chen Capital Partners"
    email: "marcus@chencap.example"
    purchasedAt: "2026-04-17T14:22:00Z"

Iterate until it looks like the real thing on a printed page.
Print preview (Ctrl+P) should produce a clean 6–8 page PDF with no
orphaned content or clipped sections.
```

### Iterate checklist

- [ ] Visually a clear sibling of the editorial memo (Wayne version),
      not a sibling of some other product.
- [ ] Utility bar looks UTILITARIAN, not promotional. A paid user has
      already closed — don't re-sell them.
- [ ] Prepared-for block reads like an engraved card, not a banner.
- [ ] Page breaks correct in print preview. No clipped tables.
- [ ] Attestation footer feels final, signed, dated. Not a "thanks
      for using our app" footer.
- [ ] No gradients, no illustrated icons, no shadow elevation.
      Hairlines only.
- [ ] Watermark shows when `purchaser` field is absent (demo mode).

### Hand back to me

Export or handoff to Claude Code. Then tell me:

> "Spec 4 output ready: [paste or path]"

I'll:

1. Extend `DealReport` interface with the optional `purchaser` block.
2. Rewrite [src/app/report/[id]/page.tsx](../deal-brain/src/app/report/%5Bid%5D/page.tsx)
   using the same `report.css` design system we built for Wayne.
3. Add Download/Share/Email handlers (PDF via `@react-pdf/renderer` or
   a simple `window.print()` for v1).
4. Backfill the demo report at `/report/demo` using the same mock data
   so a visitor without an ID still sees a polished document.
5. Build + deploy + screenshot the new version for you to approve
   before we let it ship to real customers.

---

## SPEC 5 — AI Ops Sprint Kit (the $3,500 deliverable)

**HIGH ROI.** When a client signs the $3,500 AI Ops in a Week
engagement, the final artifact they keep is the "Sprint Kit." Today
that lives under `/admin/sprint-kits/[id]` (admin-only in the current
build) and is aimed more at operators than clients. Polish this into
a client-facing keepsake and it becomes a referral magnet.

### Context

- **Product:** AI Ops in a Week — $3,500 flat, 5-day automation
  engagement for SMBs
- **Deliverable:** Sprint Kit — the final writeup the client keeps.
  Should document what was built, how to run it, ROI estimate, and
  90-day roadmap.
- **Audience:** Small-business owner / operator. Pragmatic. Will
  print it. May forward to their accountant, their partner, maybe
  their franchisor. Referral path runs through how this document
  looks.
- **Visual direction:** PRAGMATIC, NOT EDITORIAL. Different aesthetic
  from DealBrain — AI Ops is "no-BS results-first" (per the brand
  spec in the email templates: teal accent #0f766e). Think McKinsey
  implementation summary × Notion doc × IDEO project readout. LIGHT
  MODE primary (client may email it to their CFO — dark PDFs look
  weird to non-designers). Serif headlines but more contemporary
  than Fraunces — Instrument Serif, DM Serif Display, or similar.
- **Live in:** the `ai-ops-consulting` repo, route `/admin/sprint-kits/[id]`

### Paste into claude.ai/design

```
Design the "Sprint Kit" — the final deliverable document a client
keeps after a $3,500 AI Ops in a Week engagement. This is what they
read at the end of week five and what they may forward to their
business partner or CFO.

AI Ops in a Week is a 5-day automation sprint for small businesses
(SMBs: home-service contractors, small agencies, medical practices,
etc.). Day 1-2 audit current workflows, Day 3-4 build 3-5
automations, Day 5 ship live + train. The Sprint Kit documents what
was built, how to maintain it, ROI estimate, and 90-day next steps.

AUDIENCE: small-business owner, pragmatic, not a designer. Will
print this. May email to their accountant. NOT an engineer. Won't
tolerate jargon.

VISUAL DIRECTION — different from my DealBrain editorial memo, on
purpose. AI Ops needs to feel PRAGMATIC and RESULTS-FIRST, not like
an M&A pitchbook. Target reference: a McKinsey client implementation
summary crossed with a clean Notion doc. LIGHT MODE primary.

PALETTE:
- Background: warm off-white (#fafaf7) or near-white paper
- Primary text: near-black (#141414)
- Accent: deep teal #0f766e (matches AI Ops brand)
- Secondary: muted slate #64748b
- Results/positive: a compatible green-teal, restrained
- No red/amber alerts unless there's a real issue to flag

TYPOGRAPHY:
- Headlines: a contemporary serif — Instrument Serif, DM Serif
  Display, or Newsreader. Less editorial-austere than Fraunces,
  more warm/human.
- Body + data: Inter. Tabular figures for numbers.
- Labels: uppercase Inter, tight letter-spacing.

STRUCTURE (sections, in order):

1. COVER PAGE
   - Client name (big, serif)
   - "Sprint Kit" as the document kind label
   - Engagement dates
   - Small teal wordmark bottom-left: AI OPS / IN A WEEK
   - Prepared-by block, bottom-right

2. EXECUTIVE SUMMARY (1 page)
   - 3-bullet recap of what was built
   - Big number: total estimated hours/week returned
   - Big number: payback period in weeks
   - One-sentence verdict

3. THE AUDIT (1-2 pages) — what we found
   - Table of current workflows we audited: task, frequency, hours/week, owner, automation score
   - Narrative: top 3 time sinks and why

4. THE AUTOMATIONS (1 page per automation, 3-5 total)
   - Name of the automation
   - What it replaces/assists (before/after)
   - How it works (plain language: "when X happens, Y runs, Z is the output")
   - Triggers, inputs, outputs (simple diagram — boxes and arrows, no tech)
   - Who owns it going forward (client-side)
   - Pause/modify instructions
   - Estimated hours saved per week

5. ROI ANALYSIS (1 page)
   - Total hours saved/week (summed)
   - Hourly rate assumption used
   - Weekly/monthly/annual $ value
   - Payback period from $3,500 investment
   - Simple bar chart comparing before/after time allocation

6. RUNBOOK (1-2 pages) — "what could go wrong and what to do"
   - For each automation: common failure modes, how to check if it's
     running, how to pause, who to contact
   - Contact card (Kruz + phone/email)

7. 90-DAY ROADMAP (1 page)
   - 3-5 recommendations for next-tier automations the client could
     run on their own OR book another sprint for
   - Priced/scoped at high level, not a hard quote
   - This is the upsell page — keep it helpful, not pitchy

8. ATTESTATION / SIGN-OFF
   - Signed "Prepared by Kruz" block
   - Engagement end date
   - Support-window note ("30-day guarantee: if we didn't save you
     10+ hours a week, refund honored")
   - Contact details

HARD CONSTRAINTS:
- Single HTML file, inline CSS — must render in email if the client
  forwards a preview. (Production version will be a PDF export, but
  the design should not depend on JS.)
- Print-first layout. US Letter portrait. Page breaks between major
  sections. Hairline header + page number footer on every printed
  page.
- NO illustrated / stock icons. Use simple Lucide-style line icons
  or pure typography.
- NO tables with more than 6 columns. Non-designers read wide
  tables poorly.
- Client name, automation names, numbers — ALL injected as merge
  variables, format {{CLIENT_NAME}}, {{AUTOMATION_1_NAME}}, etc.
  Full list at the bottom of this brief.

MERGE VARIABLES (use these exact names, double-curly):
  {{CLIENT_NAME}}, {{CLIENT_INDUSTRY}}, {{CLIENT_CITY}},
  {{ENGAGEMENT_START_DATE}}, {{ENGAGEMENT_END_DATE}},
  {{HOURS_RETURNED_PER_WEEK}}, {{HOURLY_RATE_ASSUMED}},
  {{PAYBACK_WEEKS}}, {{ANNUAL_VALUE_USD}},
  {{AUTOMATION_1_NAME}}, {{AUTOMATION_1_DESCRIPTION}},
  {{AUTOMATION_1_HOURS_SAVED}}, (repeat for 2, 3, 4, 5),
  {{CURRENT_WORKFLOWS_AUDITED}},  (array of workflow rows)
  {{ROADMAP_ITEMS}}, {{CONTACT_EMAIL}}, {{CONTACT_PHONE}}

Render it populated with realistic data for:
- Client: "Rocky Mountain Plumbing Co." in "Colorado Springs, CO"
- Engagement: April 14-18, 2026
- Hours saved: 18/week, $125/hr, annual value $117K
- 4 automations: (1) Dispatch-to-invoice sync (Jobber → QuickBooks),
  (2) Review-request auto-send post-job, (3) New-lead triage + SMS
  reply, (4) Weekly owner digest of KPIs.

The Sprint Kit should feel like something the client is proud to
have printed and sitting on their desk. Not a SaaS dashboard, not a
sales deck — a professional client deliverable.
```

### Iterate checklist

- [ ] Cover page feels like a real document cover, not a hero section.
- [ ] Light mode. Serif headlines warmer than Fraunces (not editorial
      M&A — that's DealBrain's lane).
- [ ] Page breaks correct in print preview. Each automation on its
      own page.
- [ ] ROI numbers have visible, defensible logic (hours × rate × 52).
- [ ] Runbook is plain-language, zero jargon.
- [ ] 90-day roadmap is helpful, not a sales pitch.
- [ ] Merge variables all spelled correctly and consistently.

### Hand back to me

Export as HTML. Save to
`C:/Users/Kruz/Desktop/Projects/ai-ops-consulting/src/app/admin/sprint-kits/_template/sprint-kit.html`
and tell me:

> "Spec 5 Sprint Kit template saved"

I'll:

1. Port the HTML into a React component at
   `src/app/admin/sprint-kits/[id]/page.tsx`, replacing whatever's
   there today.
2. Wire the merge variables to the Supabase schema (add any new
   columns needed).
3. Add PDF export via a simple `window.print()` for v1 (can upgrade
   to `@react-pdf/renderer` later if clients want headless PDFs).
4. Build the client-facing route (non-admin) so the delivered link
   can go directly to the client without admin auth.
5. Generate a demo Sprint Kit for the fake "Rocky Mountain Plumbing"
   client and send you the link for approval before we point any
   real clients at it.

---

## SPEC 6 — DealBrain Main Landing Page

**MEDIUM ROI.** Brand-consistency play. Now that the editorial report
is live, the main landing page at
[deal-brain.vercel.app](https://deal-brain.vercel.app) looks like it
belongs to a different product. Anyone clicking through from Wayne's
dossier (or the new branded emails) hits a visual mismatch. Fix it
once the $199 report design (Spec 4) is settled, so the whole funnel
shares one design system.

### Context

- **Route:** `/` (landing page)
- **Current state:** zinc/emerald SaaS aesthetic, "Due Diligence in
  60 Seconds" headline, 3-step how-it-works, waitlist form at
  `#waitlist`, upload dropzone
- **What NOT to change:**
  - The URL fragment `#waitlist` (cold emails already link to it)
  - The upload-documents flow (it works, and `/analyze` builds on it)
  - Pricing structure ($0 beta, $99 early, $199 regular)
- **What TO change:**
  - Visual language — match the editorial report and /for/[slug]
    pages. Same fonts, palette, §-numbering, gold accent, hairline
    dividers.
  - Headline copy if something stronger suggests itself (not a
    requirement — the current headline works).
  - Social proof placement — we have Wayne's response as a real
    signal; if that can be surfaced tastefully ("brokers with 20+
    yr certs have already engaged"), it's worth including.

### Paste into claude.ai/design

```
Redesign the marketing landing page for DealBrain to match the
editorial design system I just shipped for the customer-facing
report. This page lives at deal-brain.vercel.app — it's the first
page every cold-email click and every referral lands on.

DealBrain is an AI-powered due diligence tool for small-to-mid
market business acquisitions ($1-25M revenue range). The buyers
who pay for it are senior brokers and operators, not VCs or
consumers.

USE THE SAME DESIGN SYSTEM as the editorial report I already have
live at /for/wright-business-advisors:
- Charcoal background (#0b0b10), paper-cream foregrounds
- Burnt-gold accent (oklch(0.80 0.12 75))
- Fraunces serif headlines + Inter body + JetBrains Mono for IDs
- §-numbered sections with hairline rule dividers
- No gradients, no shadow elevation, no illustrated icons

SECTIONS (in order):

1. MINIMAL TOP BAR
   - DealBrain wordmark (serif, tracking tight) + "·Reports"
     subtitle, gold
   - Nav right: "Sample Report" link · "Pricing" · "Sign in"
   - NO "get started" button — this is an editorial site, not a SaaS

2. HERO
   - Eyebrow: "DUE DILIGENCE · FOR SMALL-TO-MID MARKET DEALS"
   - Big serif headline (Fraunces, tracking-tight, ~72px):
     something that improves on today's "Due Diligence in 60
     Seconds." Suggest 3 headline options I can pick from — aim
     for confidence and clarity, not urgency.
   - Subhead (Inter, 18px, paper-1): two lines, what it does and
     who it's for.
   - Primary CTA: "See a sample report" — gold text underline,
     not a button. Leads to /sample.
   - Secondary: "Upload deal documents" — opens the upload flow.
   - Right side: a small editorial vignette — a fragment of the
     deal-score gauge + a finding card, stylized, under a
     "What you get" label. NOT a screenshot. NOT a hero
     illustration. A DESIGNED fragment.

3. §01 — HOW IT WORKS (replaces current "3-step")
   Three numbered cards in a horizontal row:
   §01.1 Upload — drop CIM + financials + lease + contracts
   §01.2 Analyze — three AI agents work in parallel
   §01.3 Report — deal score, risk matrix, analyst verdict
   Each card: thin hairline border, serif headline, 2-sentence
   description. Numbers in the same style as the memo.

4. §02 — ONE REAL FINDING
   A single rendered-fragment example of a DealBrain finding,
   taken from the Summit Precision Machining sample (the customer
   concentration one is the strongest). Presented as a framed
   "finding card" with severity badge and citation. Above it,
   a pull quote from the sample: "Top customer = 41% of revenue
   on a 90-day-terminable MSA."
   Below it: a soft "See the full sample report" link.

5. §03 — SOCIAL PROOF
   Two or three quotes or signals, restrained. E.g.:
   - "MCBI brokers have already engaged in week one of beta."
     (Wayne's reply is the real signal — don't name him, just
     reflect the category.)
   - A certification/tech signal: "Powered by Claude Opus 4.6"
     or similar, if tasteful.
   NO big logo parade. No avatar grid. Hairlines and text only.

6. §04 — PRICING (replaces current pricing section)
   Three tiers, memo-card style (NOT a SaaS pricing table):
   - Beta (Free) — "limited to 20 reports in April 2026"
   - Early Adopter ($99/report) — "locked in through 2026"
   - Standard ($199/report) — "after beta closes"
   Each tier: serif price, gold if the CTA is on that tier,
   hairline dividers. One-line value prop per tier. No feature
   matrix with checkmarks.

7. §05 — WAITLIST (anchor: #waitlist, DO NOT change the hash)
   - Small serif headline: "Get early access."
   - One-sentence rationale
   - Two inputs: email (required), name (optional). Gold
     primary action button. Minimal.
   - Below: "No credit card. Unsubscribe anytime."

8. §06 — FAQ (condensed, 5 questions max)
   Expandable <details> elements with hairline top-borders.
   Short answers. No jargon.

9. FOOTER
   Copyright + a couple of links + contact. Hairline top. Gold
   wordmark. Muted text.

HARD CONSTRAINTS:
- PRESERVE the `#waitlist` URL fragment — cold emails in the wild
  link to it. Moving it breaks campaigns already shipped.
- Keep the upload-document affordance. The existing /analyze flow
  expects it to exist on the home page.
- Single React component or file structure compatible with the
  existing Next.js App Router project.
- Same design system (report.css) we built for /for/[slug] — the
  main landing should be a natural continuation, not a new thing.
- NO testimonial avatars, NO logo parade, NO "trusted by" bar, NO
  stock photos. This is editorial-grade, not generic SaaS.
- Mobile: collapses cleanly to a single column.

Render three headline options for the hero — keep the rest of the
page design as one version.
```

### Iterate checklist

- [ ] Visually a SIBLING of the editorial report and the /for/[slug]
      pages. Same system.
- [ ] `#waitlist` anchor still works and form is visible there.
- [ ] Hero doesn't look like a SaaS hero. No cartoon. No gradient blob.
- [ ] Pricing section isn't a checkmark matrix.
- [ ] Social proof restrained — no avatars, no logos.
- [ ] Mobile works.
- [ ] Gold accent, not emerald. Anywhere.

### Hand back to me

Handoff to Claude Code or export React. Then:

> "Spec 6 output ready"

I'll:

1. Replace [src/app/page.tsx](../deal-brain/src/app/page.tsx) with
   the new design.
2. Move the existing waitlist + upload logic into the new component
   structure (the functional parts stay, only visuals change).
3. Update [src/app/layout.tsx](../deal-brain/src/app/layout.tsx) so
   the global header matches the new aesthetic.
4. Keep `/sample` (the existing generic sample) working but update
   its styling so everything shares one visual system.
5. Deploy, screenshot the full funnel: landing → sample → waitlist
   → responder-custom page → paid report. Send it all back for
   approval before it becomes the default face of the product.

---

## Which to run first

Unless you have other input: **Spec 4 first.** It's the experience
a paying customer gets — highest leverage per hour of your time in
Claude Design.

Then 5 (Sprint Kit), then 6 (main landing).

## Notes on running these in Claude Design

- For Spec 4, **attach the `deal-brain` codebase** so Claude Design
  can see the existing `DealReport` interface and the `report.css`
  we just shipped. Mode: "Front-end design wireframe."
- For Spec 5, mode: "Save as standalone HTML" (this is ultimately a
  PDF-first deliverable, not a React app — HTML is more portable).
- For Spec 6, attach the `deal-brain` codebase again, mode: "Front-
  end design wireframe." Same handoff flow as Spec 1.
