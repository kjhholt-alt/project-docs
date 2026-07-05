```
PROJECT: Outdoor CRM (AATOS) — Full Audit, Polish & Client Handover Sprint
REPO: Existing repo `outdoor-crm` in C:\Users\Kruz\Desktop\Projects\

## PIPELINE INSTRUCTIONS
- Use the GitHub MCP to push all code after each phase
- Use the Vercel MCP to verify frontend deployment after pushing
- Use the Railway MCP to verify backend deployment
- Run all tests before final commit
- Create/update CLAUDE.md as the last step
- After everything is built and tested, commit with a clear message and push to main

## CONTEXT

This is a CLIENT PROJECT for a general outdoor services company (lawn care, landscaping, snow removal, tree service, etc — multiple service types). The stack is React 19 + Vite + Django 5 + DRF. It has basic CRUD already — contacts, jobs, and invoices exist but need polish.

THE GOAL: Get this CRM polished enough to invoice the client and hand it over. This is NOT a rebuild. This is an audit → fix → polish → ship sprint. Do not add features that aren't listed below. Do not refactor working code for style reasons. If it works, leave it alone and move on.

## PHASE 1: AUDIT (Do First — Touch Nothing Until This Is Done)

### 1.1 Codebase Audit
Before changing ANYTHING, do a full read of the codebase and produce a report:

- List every Django model with its fields and relationships
- List every API endpoint (URL, method, serializer, permissions)
- List every React page/component and what it does
- List every feature that EXISTS and WORKS
- List every feature that EXISTS but is BROKEN (with the specific bug)
- List every feature that is MISSING but obviously needed (e.g., a "delete" button exists in the UI but the API endpoint 404s)
- Check auth: does login/logout/registration work? JWT or session?
- Check permissions: can users only see their own data? Is there RLS or Django permissions?
- Check the database: are there migrations? Any pending migrations? Any orphaned models?
- Check deployment: is there a Procfile/railway.json? Is the frontend deploy config correct?
- Check for hardcoded values, localhost URLs, test credentials left in code
- Check for console.log spam, debug print statements, TODO comments

Output this as: `docs/AUDIT_REPORT.md`

DO NOT FIX ANYTHING YET. Just document. I need to see the full picture before you start changing things.

### 1.2 Read PROPOSAL.md
The repo has a PROPOSAL.md file. Read it. This is what was promised to the client. Cross-reference against what actually exists. Document any gaps between what was proposed and what was delivered. Add this to the audit report as a "Proposal vs Reality" section.

## PHASE 2: FIX WHAT'S BROKEN (Do Second)

Based on the audit, fix every bug you found. Priority order:

### 2.1 Critical (App-Breaking)
- Auth not working
- Pages that crash/white-screen
- API endpoints that 500
- Data not saving
- CORS errors between frontend and backend

### 2.2 High (Feature-Breaking)
- CRUD operations that don't work (create works but edit doesn't, delete 404s, etc.)
- Forms that submit but data doesn't persist
- List views that don't load data
- Search/filter that doesn't work
- Pagination broken

### 2.3 Medium (Polish)
- UI elements that look broken (overlapping text, wrong colors, missing icons)
- Empty states missing ("No contacts yet — add your first one")
- Loading states missing (buttons should show spinner while API calls are in flight)
- Error states missing (show toast/alert when something fails, not silent failure)
- Mobile responsiveness issues

### 2.4 Cleanup
- Remove all console.log / print() debug statements
- Remove all hardcoded localhost URLs — use environment variables
- Remove any test/seed data or demo credentials
- Fix any TypeScript errors / Python linting issues
- Ensure all API responses have consistent shape

Commit after this phase: "fix: resolve all bugs found in audit"

## PHASE 3: POLISH FOR HANDOVER (Do Third)

These are the features that make the difference between "a dev project" and "a product I can hand to a client." Only build what's listed here.

### 3.1 Dashboard (Home Page After Login)
If a dashboard doesn't exist or is bare, build one with:
- **Summary cards**: Total Contacts, Active Jobs, Revenue This Month, Outstanding Invoices
- **Recent activity feed**: "Invoice #1042 sent to John Smith" / "New job created: Spring Cleanup at 123 Oak St"
- **Upcoming jobs**: Next 7 days of scheduled jobs with client name, service type, date/time, address
- **Revenue chart**: Simple bar chart of monthly revenue (last 6 months) using Recharts
- Keep it clean and simple. This is the first thing the client sees when they log in.

### 3.2 Contacts / Customers
Ensure the full CRUD flow works perfectly:
- **List view**: Searchable, sortable table. Columns: Name, Phone, Email, Address, Total Jobs, Last Service Date, Outstanding Balance. Click row → detail view.
- **Detail view**: Contact info card + tabs for Jobs History, Invoices, Notes
- **Quick actions**: "New Job" button on contact detail (pre-fills client), "Send Invoice" button
- **Notes**: Simple text notes with timestamp and author, chronological. Add note form at top.
- **Import**: CSV import for contacts (name, phone, email, address). Simple file upload → parse → preview → confirm. Client will want to import their existing customer list.

### 3.3 Jobs / Work Orders
This is the core of the CRM. A "job" = a service visit (mow lawn, trim trees, plow driveway, etc.)

- **Job creation**: Client (dropdown from contacts), Service Type (dropdown: Lawn Mowing, Landscaping, Tree Trimming, Snow Removal, Leaf Cleanup, Irrigation, Hardscaping, General Maintenance — make this configurable in settings), Scheduled Date/Time, Address (default from client's address), Estimated Duration, Price/Quote, Notes, Status (Scheduled → In Progress → Completed → Invoiced → Cancelled)
- **Job list**: Filterable by status, service type, date range, client. Default view: upcoming jobs sorted by date.
- **Calendar view**: Monthly/weekly calendar showing scheduled jobs. Color-coded by service type. Click a day → see that day's jobs. Click a job → detail view. Use a lightweight calendar component (react-big-calendar or @fullcalendar/react or build a simple custom one with a grid).
- **Job detail**: All info + ability to edit, add photos (before/after — upload to Django media or S3), log actual time, mark complete, generate invoice from job.
- **Recurring jobs**: "Repeat: Weekly / Bi-weekly / Monthly / Custom." When a recurring job is created, auto-generate the next N occurrences (e.g., next 12 weeks of weekly mowing). Each occurrence is its own job record that can be individually edited or cancelled.
- **Route sheet / Daily view**: For a given date, show all jobs in order with addresses. This is what the crew prints out or pulls up on their phone in the morning. Show: time, client name, address, service type, special notes.

### 3.4 Invoicing
- **Generate from job**: Click "Invoice" on a completed job → auto-populates line items from the job (service type, hours, rate, total). Allow editing before sending.
- **Generate manually**: Create a blank invoice, add line items manually.
- **Invoice fields**: Invoice number (auto-increment), client, date, due date, line items (description, qty, rate, amount), subtotal, tax rate (configurable in settings), tax amount, total, notes/terms, status (Draft → Sent → Viewed → Paid → Overdue → Void).
- **Invoice list**: Filterable by status, client, date range. Show total outstanding, total paid this month.
- **PDF generation**: Generate a clean, professional PDF invoice. Use ReportLab or WeasyPrint on the Django side. Include: company logo (uploaded in settings), company info, client info, line items table, totals, payment terms, due date. The PDF should look like it came from QuickBooks, not a developer's side project.
- **Mark as paid**: Button to mark invoice paid with payment date and method (Cash, Check, Card, Transfer). No actual payment processing needed — this is a small local business, they get paid in cash/check/Venmo mostly.
- **Email invoice**: Send the PDF to the client's email. Use Django's email backend (SMTP). Include a simple message: "Hi [name], here's your invoice for [service]. Total: $[amount]. Due: [date]."
- **Overdue detection**: Invoices past due date auto-flag as "Overdue". Show overdue count on dashboard.

### 3.5 Estimates / Quotes
- Separate from invoices. An estimate is a quote that can be accepted → converted to a job + invoice.
- **Estimate fields**: Same line items as invoices but status is: Draft → Sent → Accepted → Declined → Expired.
- **Convert to job**: When client accepts, one-click convert estimate → job (pre-filled) + optionally generate invoice.
- **If this feature doesn't exist, build a basic version.** If it exists, make sure the flow works end to end.

### 3.6 Settings Page
- **Company info**: Business name, address, phone, email, logo upload
- **Tax rate**: Default tax percentage for invoices
- **Service types**: Add/edit/delete service types (the dropdown options for jobs). Default seed: Lawn Mowing, Landscaping, Tree Trimming, Snow Removal, Leaf Cleanup, Irrigation, Hardscaping, General Maintenance
- **Invoice terms**: Default payment terms text (e.g., "Net 30. Late payments subject to 1.5% monthly interest.")
- **Invoice numbering**: Starting number, prefix (e.g., "INV-")
- **Email settings**: SMTP config (or just use Django's default email backend)

### 3.7 Search
- Global search bar in the header/nav
- Searches across: contacts (name, email, phone), jobs (address, notes), invoices (invoice number, client name)
- Results grouped by type with links to detail views

### 3.8 Mobile Responsiveness
- The client and their crew will use this on phones in the field
- Priority mobile views: Daily route sheet, job detail (mark complete, add photos), contact lookup (find phone number)
- Navigation: hamburger menu on mobile, bottom nav bar if it fits the design
- Tables → card views on mobile

## PHASE 4: DEPLOYMENT & HANDOVER (Do Last)

### 4.1 Environment & Deploy
- Backend (Django): Verify Railway deployment works. Ensure all env vars are documented.
- Frontend (React/Vite): Verify Vercel deployment works. Ensure API URL is environment-configured (not hardcoded).
- Database: Verify migrations are clean. Run `python manage.py migrate` with no errors.
- Media files: If using photo uploads, ensure media storage works (S3, Railway volume, or Cloudflare R2).
- CORS: Verify frontend ↔ backend communication works in production (not just localhost).

### 4.2 Seed Data
Create a management command: `python manage.py seed_demo_data` that generates:
- 15 sample contacts with realistic names, addresses, phone numbers
- 30 sample jobs across different service types and statuses
- 10 sample invoices in various states (draft, sent, paid, overdue)
- 3 sample estimates
This lets the client see the CRM populated when they first log in. They can delete the seed data when they start adding their own.

### 4.3 Documentation
Create/update these files:

**README.md** (for the client)
- What this CRM does (2-3 sentences)
- How to access it (URL, login credentials)
- Quick start guide: "Here's how to add your first customer and create a job"
- Feature list with screenshots (if possible) or descriptions
- Support contact (your email/phone)

**CLAUDE.md** (for future Claude Code sessions)
- Full project context
- Every model, endpoint, page
- Deploy commands for both frontend and backend
- Environment variables needed
- Database migration commands
- Known quirks or limitations

**docs/HANDOVER.md** (for the client)
- Account setup: how to change the admin password, add their company info/logo
- Importing contacts: CSV format expected, how to upload
- Daily workflow: "Morning: check dashboard → print route sheet → go to jobs → mark complete → send invoices"
- FAQ: common questions a non-technical business owner would have

### 4.4 Final QA Checklist
Before committing the final code, verify ALL of these manually:

- [ ] Can register a new account
- [ ] Can log in and see the dashboard
- [ ] Can create a new contact
- [ ] Can edit and delete a contact
- [ ] Can create a new job for a contact
- [ ] Can view jobs in list view
- [ ] Can view jobs in calendar view
- [ ] Can mark a job as complete
- [ ] Can generate an invoice from a completed job
- [ ] Can manually create an invoice
- [ ] Can mark an invoice as paid
- [ ] Can generate and download invoice PDF
- [ ] Can create an estimate
- [ ] Can convert estimate to job
- [ ] Can upload company logo in settings
- [ ] Can configure service types in settings
- [ ] Can search for a contact by name
- [ ] Dashboard shows correct summary numbers
- [ ] Mobile: can view route sheet on phone-sized screen
- [ ] Mobile: can mark a job complete on phone
- [ ] No console errors in browser dev tools
- [ ] No 500 errors in Django logs
- [ ] All API endpoints return proper error messages on bad input

If ANY of these fail, fix them before the final commit.

## RULES

1. DO NOT rebuild the project from scratch. Work with what exists.
2. DO NOT change the tech stack. It's React 19 + Vite + Django 5 + DRF.
3. DO NOT add features not listed above. The scope is locked.
4. If something works, don't refactor it for style. Move on.
5. Commit after each phase with a clear message.
6. If the existing code has a pattern (e.g., how serializers are structured, how components are organized), follow that pattern. Don't introduce a new architecture.
7. If you need to add a library, prefer small/lightweight ones. Don't add a massive UI framework — work with whatever CSS/component library is already in the project.
8. Every form must have validation. Every API call must have error handling. Every list must have empty states and loading states.
9. The client is a small business owner, not a developer. The UI must be intuitive. Labels should be plain English ("Add Customer" not "Create Contact Entity"). Icons should be obvious.
10. Test your changes. If the project has existing tests, run them. If not, at minimum write tests for invoice calculations (tax math must be correct) and job status transitions.

## SUCCESS CRITERIA

1. Audit report documents full codebase state
2. All bugs from audit are fixed
3. Dashboard shows real summary data
4. Full contact → job → invoice flow works end to end
5. Calendar view shows scheduled jobs
6. Invoice PDF looks professional
7. Estimates can convert to jobs
8. Settings page lets client configure their business
9. Search works across contacts, jobs, invoices
10. Mobile responsive — crew can use it in the field
11. Seed data command populates realistic demo data
12. README, CLAUDE.md, and HANDOVER.md are complete
13. QA checklist passes 100%
14. Deployed and accessible at production URLs

## WHAT DONE LOOKS LIKE

When this is finished, I can:
1. Send the client a URL and login credentials
2. Walk them through a 10-minute demo
3. Hand them the HANDOVER.md doc
4. Send them an invoice for the work
5. Move on

The CRM should feel like a real product, not a half-finished dev project. Professional enough that the client shows it to their business partner and says "look what we got built."
```
