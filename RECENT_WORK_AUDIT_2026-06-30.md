# Recent Work Audit — 2026-06-30

Read-only QA + code audit of every repo/lane that saw real activity in the last ~1–2 weeks. Verified against the actual code on disk; findings cite `file:line`. Severity tags: **[High]** = fix before shipping / real exposure, **[Med]** = correctness or security gap worth a near-term fix, **[Low]** = polish / latent risk.

**Scope:** 6 repos with genuine recent code activity were deep-audited (swe-agent-platform, llm-benchmark-suite, research-agent-platform, pc-bottleneck-analyzer, wishlist-tracker, agent-os) + the operator-suite/GambaTime lane. `ledger` and `deal-brain` were checked and excluded (see notes). `cornerstone` skipped per instruction (moving target).

---

## Meta-finding — operator-suite working tree is badly diverged (parked branch)

The Projects-root checkout is parked on branch `chore/magnum-opus-phase-0-1-docs`, which is **302 commits behind `master` and 123 commits ahead**. Consequences that colored this audit:

- `research-agent-platform/` and `agent-os/` are subdirectories tracked *inside* operator-suite (not standalone repos). Their real Phase-1 code is on **`master`**. On disk, `research-agent-platform/` has full code (matches), but `agent-os/` has **docs only** — its code was audited via `git show master:agent-os/...`.
- On-disk clipforge/GambaTime code is "as of the parked tree" and may not equal what actually runs from `master`. Audited on disk with that caveat.
- **123 local commits not on master could be stranded.** Worth a conscious decision: rebase/merge the parked branch or confirm those 123 commits are disposable. This is a portfolio-hygiene risk independent of any single project.

---

## swe-agent-platform  (standalone repo — Devin-like multi-agent coder)

**Maturity:** Real Phase-2 implementation, not scaffold. **83 tests pass** (79 fast + 4 e2e; run via `.venv/Scripts/python.exe -m pytest --runslow` — the global `py` launcher lacks `langgraph`). Eval harness genuinely runs → grades → reports, fixtures carry real seeded bugs, STATUS.md is unusually honest. No shell injection (argv-only subprocess, tested against `$(injection)`), path-traversal guarded, no committed secrets, no TODO/FIXME, all loops bounded.

- **[Med] PR base branch hardcoded to `main` — silently fails on `master` repos.** `src/swe_agent/tools/github_tools.py:36` defaults `base="main"`; `graph.py:60` never passes a base. `gh pr create --base main` errors on any repo whose default branch isn't `main` (including this one). `_finalize_node` swallows the error (`graph.py:64-67`) so the run reports "done" with no PR. *Fix: detect default branch (`gh repo view --json defaultBranchRef`) and pass it.*
- **[Med] Finalize can push straight to the default branch.** `_finalize_node` pushes `current_branch()` (`graph.py:53-54`); the coder is only *instructed* to branch (`nodes/coder.py:40`), nothing enforces it. If the LLM commits on `master`, finalize does `git push -u origin master`. *Fix: assert `current_branch != default_branch` before push, else bail.*
- **[Med] Reviewer "blocking" gate is soft — gives up after 3 bounces and ships anyway.** `src/swe_agent/nodes/reviewer.py:96-110`: after `MAX_REVIEW_BOUNCES`, an unresolved *blocking* finding proceeds to tester→finalize instead of stopping. The tester checks tests, not artifacts, so nothing re-blocks. *Fix: on give-up with unresolved blocking findings, route to `END` (human review).*
- **[Low] Silent finalize failure masks push/PR errors as success.** `graph.py:64-68` returns `phase:"done"` even on `GitError`/`GitHubError`; CLI exits 0. *Fix: set a `finalize_failed` flag, exit non-zero.*
- **[Low] `ALL_CHECKS` has exactly one deterministic reviewer check** (tracked `.pyc`, `nodes/checks.py:76`), so the multi-agent reviewer barely earns lift over the single agent — the whole Phase-2 thesis. *Add a second real check (e.g. new file with zero test coverage).*

**Ideas:** (1) Fix the two finalize gaps + non-silent failure together (~20 lines, and a broken finalize would corrupt the `multi` arm's solved-count in the Phase-2 measurement). (2) Add a second deterministic reviewer check to make multi-agent lift measurable. (3) Route "no test command found" to a terminal state, not the debugger (it can't fix a missing runner). (4) Add `eval/config.toml` `{repeats, win_margin, task_set}` so the gated measurement run is one command, not a fresh methodology discussion.

---

## llm-benchmark-suite  (standalone repo — agent-powered LLM eval platform)

**Maturity:** Real Phase-1/early-2 slice (orchestrator + 5 standard benchmarks + single-judge LLM-as-judge + calibration harness), **84 tests** (pass inside `.venv`; 3 orchestrator tests fail only under the bare `py` launcher — environmental, langgraph 1.2.7 is installed in the venv). But ~30 directories are empty `.gitkeep` placeholders (all of `agents/analyzers`, `benchmark_generators`, `meta_development`, `benchmarks/{agentic,safety,efficiency,robustness,custom}`, `evaluation/adaptive`, `dashboards`, etc.). No committed secrets.

- **[High] Duplicate item IDs corrupt the calibration correlation — the Phase-2 done-gate metric.** `tools/reporting/make_grading_sheet.py:32-36` pools two runs of the *same* 10 GSM8K items (`gsm8k_openai_compatible_proof.json` + `gsm8k_hf_local_proof.json`, both `seed=0, num_items=10`, identical IDs `gsm8k-test-0..9`), producing a 20-row sheet with only 14 unique IDs (6 duplicated). `agents/judges/calibration.py:266` builds `by_id = {s.id: s for s in samples}` — a 1-key-per-id dict — so on read-back the second CSV row for a duplicated ID overwrites the first, and `correlation()` (line 309) pairs mismatched judge/human scores. **The headline judge-vs-human number is computed on corrupted pairings.** *Fix: namespace IDs by source/adapter when pooling, or key `by_id` on `(source, id)`; dedupe before sampling.*
- **[High] No test covers the duplicate-ID path** where the bug above lives (`tests/test_calibration.py` uses only unique IDs). The one bug that corrupts the flagship metric has zero regression coverage. *Fix: add a duplicate-ID calibration test.*
- **[Med] Multiple-choice answer extraction scores the *first* mentioned letter, not the chosen one.** `benchmarks/standard/mmlu_pro.py:79-105` and `bbh.py:63-95`: after `"answer is (X)"` fails, the fallback grabs the first standalone A–J letter — so a model that reasons "A is wrong, B is right" scores **A**. No "unparseable" state; a parse miss silently becomes a wrong answer. Can swing reported accuracy by tens of points on reasoning-heavy models. *Fix: anchor to the *final* answer (last match), and bucket unparseable separately from wrong.*
- **[Med] IFEval passes unknown instruction IDs as "followed," inflating scores.** `benchmarks/standard/ifeval.py:177-210` implements 16 of ~25 real `google/IFEval` instruction IDs; unknown IDs default to compliant (`_lenient_unsupported=True`). Any prompt with an unimplemented instruction is scored fully compliant on it. The `unsupported` fraction is captured in `detail` but never surfaced. STATUS also admits IFEval + BBH have no live proof-of-life run. *Fix: surface unsupported-fraction per run; implement the missing checkers or treat unsupported as not-followed for the headline.*
- **[Med] HumanEval executes model-generated code with no sandbox** — `benchmarks/standard/humaneval.py:93-103` runs candidate + test via `subprocess.run([sys.executable, ...])`, timeout-bounded but no FS/network/syscall isolation (classic HumanEval RCE). It **is** honestly documented (docstring, README, STATUS) so it's known-accepted, but it's the biggest exposure for a tool meant to point at untrusted models. *Fix: gate behind `--allow-code-exec`; run under a container/firejail before pointing at untrusted models.*
- **[Low] Claude reached via OpenAI-compat shim, not the `anthropic` SDK** (`agents/judges/defaults.py:50,67-74` → `claude-sonnet-4-6` over `api.anthropic.com/v1/` using the `openai` client). Model id is valid and the `top_p` omission fix (`openai_compatible.py:53-58`) is **correct** (Anthropic's OpenAI endpoint rejects temperature+top_p together). Deliberate "one adapter for any OpenAI-wire backend" choice; forgoes first-party features (thinking, token accounting). Fine for an internal judge — flag if the judge becomes load-bearing.
- **[Low] Aggregation has no abstention/error bucket** — `metrics/core/aggregate.py:37` counts every non-correct as wrong, so transient API errors + parse misses understate capability. Dead `errored` var at `make_grading_sheet.py:88`.

**Ideas:** (1) Fix the pooling ID collision structurally (carry source into `CalibrationSample` identity — also fixes latent wrong-prompt recovery). (2) Surface parse-failure / unsupported-fraction into the report — "how many couldn't be graded" matters as much as accuracy. (3) Add Wilson/bootstrap CIs to `aggregate()` (cheap, directly serves the "not point estimates" ROADMAP bar; naked "40%" on N=10 is misleading). (4) Add a `conftest.py` `importorskip("langgraph")` guard so the venv-vs-launcher mismatch stops looking like a real failure.

---

## research-agent-platform  (code on disk + master — deep-research → cited reports)

**Maturity:** Genuinely working Phase-1 — pipeline runs end-to-end, produces a real 10-source cited report with correct deterministic source attribution, FTS5/vector KB is sound (triggers verified consistent), grounding is real (LLM cites `[n]`, Sources list built from the KB not the model). **KB test suite: 4 passed** (`.venv/Scripts/python.exe`; the `py` launcher fails collection — package is editable-installed only into `.venv`). The 2 network+paid tests were deliberately not run.

- **[Med] Real API keys sit in plaintext `.env` on disk — but correctly gitignored and NOT committed.** `research-agent-platform/.env:1-2` holds a real `ANTHROPIC_API_KEY` (`sk-ant-api03-…`, full length) and real `FIRECRAWL_API_KEY`. Verified: `.gitignore:5` covers `.env`, `git ls-files` does not track it, and `git log -S` for both fragments is **empty** (not in history). Local-disk exposure only → Med, not High. *Fix: fine for local dev; rotate both before this repo ever gets a shared/public remote.*
- **[Med] Synthesis citations are never validated against the real source set — a hallucinated `[n]` ships silently.** `graph/nodes.py:191-220` *tells* the model not to cite out-of-range numbers but nothing enforces it; `report/builder.py:19-33` pastes the body verbatim and appends a Sources list of `len(summaries)`. A `[11]` when 10 sources exist (or a mis-attributed claim) renders with zero detection — fatal for a tool whose whole value is "every claim traces to the right source." *Fix: regex the `[n]` markers post-synthesis, assert `1 ≤ n ≤ len(summaries)`, raise/log on violation.*
- **[Med] `MIN_SOURCES_TARGET` is enforced as a hard *max*, silently capping at 10 and dropping sources.** `graph/nodes.py:127-128` breaks the loop at `>= MIN_SOURCES_TARGET`; there's also no floor guarantee, so failed fetches can yield **fewer** than 10 and the run still "succeeds" with a warning. *Fix: iterate up to a separate `MAX_SOURCES`, treat the minimum as a post-condition assertion.*
- **[Med] `test_pipeline_e2e.py` is real but asserts almost nothing about grounding** — `tests/test_pipeline_e2e.py:24-32` only checks `source_count >= 10`, `"## Sources"` present, and `>0` citation markers. It would pass even if every citation were misattributed. *Fix: assert `max([n]) <= source_count` and unique Source URLs — also covers the citation-validation gap above.*
- **[Low] SSRF surface in `fetch_pdf_text`/`fetch_markdown`** — `tools/pdf_extract.py:12` (`follow_redirects=True`) and `tools/fetch.py:19-24` fetch URLs from search results with no scheme/host restriction or private-IP block. Low today (URLs come from Firecrawl/arXiv, not direct user input). *Fix: restrict to `https` + arxiv host, block redirects to private ranges.*
- **[Low] `SYNTHESIS_MODEL=claude-sonnet-5` is an unversioned/fragile id** (`config.py:23`, `.env.example:7`) while the summarizer uses a dated id. If the alias doesn't resolve, `synthesize_node` dies at the last step. *Fix: pin a dated, known-valid synthesis model id.*
- **[Low] Tests only pass inside `.venv`** — package is editable-installed there; `py -m pytest` fails collection (`ModuleNotFoundError: research_agent`). *Fix: add `pythonpath = src` under `[tool.pytest.ini_options]` or document the venv requirement.*

**Ideas:** (1) Add a claim→source verifier node — the KB already stores each claim with a `derived_from` edge (`kb/store.py:106-117`); confirming each `[n]` sentence actually appears in source *n* turns "grounded in principle" into "grounded and verified" (the real differentiator vs plain RAG). (2) Use the hybrid search you already built — `search_hybrid`/`search_vector`/`search_fts` (`kb/store.py:127-166`) are implemented, tested, and **unused**; wiring KB-first dedup would stop re-paying Firecrawl+Anthropic on re-runs. (3) Parallelize fetch/summarize (`nodes.py:126` is fully sequential; sources are independent → ~10x wall-clock). (4) Persist `errors` + `report_path` on the `Report` model so callers know when a report is thinner than intended.

---

## pc-bottleneck-analyzer  (standalone repo — LIVE at pcbottleneck.buildkit.store)

**Health:** Reasonably solid for a live SEO/affiliate site — **`tsc --noEmit` clean, 16/16 tests pass**, the prior "autopilot broke prod" incident has a genuine fix (fault-tolerant MDX loader is on the hot path), no secrets leaked to GitHub. Fragility is concentrated in ungated public API routes and content hygiene.

- **[High] `/api/analyze` is public, unauthenticated, unthrottled — burns your Claude sub / Anthropic key on demand.** `src/app/api/analyze/route.ts:43-77` accepts any POST, builds a prompt from fully user-controlled `scan`/`analysis`/`messages` (lines 59-71), runs it through `claude -p` via `ask()`. No auth, no rate limit, no origin check → anyone can loop it to drain quota (or real $ if `ANTHROPIC_API_KEY` is set in Vercel env), and fully controls the conversation turns (prompt injection into your authenticated Claude session). OS-shell injection is *not* possible (`claudex.js:77` spawns `shell:false` with array args — good). *Fix: per-session token or `ip_hash` rate limit + origin check + cap `messages` size.*
- **[High] Live `ANTHROPIC_API_KEY` in `.env.local`, contradicting the project's own "no API key" design.** `.env.local:1` has a real `sk-ant-api03-…`; `.env.example:4-5` says AI runs through claudex with no key, and the analyze route only uses claudex — so the key is unreferenced yet live on disk. Verified **never committed** (`git log --all -S` and `-- .env.local` both empty; `.gitignore:38` covers `.env*`). *Fix: rotate (it's been sitting plaintext) and delete from `.env.local`; if it's ALSO set in Vercel env, that's what makes `/api/analyze` cost real money.*
- **[High] Zero automated tests on the core analyzer, and the autopilot pushes to `main` autonomously.** Only `src/lib/__tests__/tier-list.test.ts` exists (16 tests). The 15-rule bottleneck engine (`analysis.ts` — the actual product), `compare.ts`, `fps-estimator.ts`, `percentile.ts`, `gpu-for-game.ts`, and the `pcpartpicker.ts` HTML parser have **zero tests**, yet the SEO autopilot commits straight to `main` with only `next build` as the gate. *Fix: Vitest on the pure libs (start with `analysis.ts` + the parser), wire as a required check before the autopilot push.*
- **[Med] `/stats` auth stores the raw secret as the cookie value and reuses `CRON_SECRET`.** `src/app/api/stats-auth/route.ts:31` sets `stats_auth = secret`; `stats/page.tsx:122` compares `cookie === secret`. `httpOnly`+`secure` (so not JS-readable), but the browser persistently holds your actual `CRON_SECRET`, which also guards the cron endpoint (`api/analytics/discord/route.ts:18`). *Fix: store an HMAC of the secret in the cookie; use a dedicated `STATS_SECRET` distinct from `CRON_SECRET`.*
- **[Med] External autopilot re-publishes the same slug ~40+ times with no dedupe.** `git log --follow -- src/content/blog/is-my-gpu-bottlenecking-my-cpu-find-out-now.mdx` shows 40+ commits each rewriting the *same* file (~120 ins/120 del, "1 file changed"). **This answers the duplicate-slug question: it is NOT creating duplicate URLs — it repeatedly regenerates one existing post's body.** Harm is churn (spammed history, risk of resetting `publishedAt`), not dup pages. Generator lives externally (operator-scripts / scheduled task, not in this repo). *Fix (in the autopilot): skip regeneration if the slug already exists / diff is cosmetic; add a "topic already covered" guard.*
- **[Med] Three near-identical GPU-bottleneck posts = keyword cannibalization.** Same topic at `src/data/blog-posts.ts:16` (TSX) + `is-my-gpu-bottlenecking-my-cpu-heres-how-to-tell.mdx` + `is-my-gpu-bottlenecking-my-cpu-find-out-now.mdx`; two share essentially the same title. Google splits ranking signal on your highest-intent keyword. *Fix: pick one canonical, 301/`alternates.canonical` the others.*
- **[Low] `/compare/<a>-vs-<b>` renders nonsensical cross-component matchups.** `src/app/compare/[slug]/page.tsx:88-92` resolves any two parts with no same-component check, though `compare()` computes `sameComponent` (`lib/compare.ts:120`) and nobody reads it → `/compare/rtx-5070-vs-ryzen-7-9800x3d` yields "GPU is N% faster than CPU," indexable junk. *Fix: `if (!both || both.a.component !== both.b.component) notFound();`*
- **[Low] `GET /api/scan` returns one global "latest scan" to everyone** — `src/app/api/scan/route.ts:13-14,107-121` stores a single shared `/tmp/latest-scan.json` with no per-user key, so any visitor gets the last person's scan. Non-PII + ephemeral, but a cross-visitor bleed. POST side is well-hardened (2MB cap, shape validation, prototype-pollution rejection). *Fix: key by `scan_id`, require it on GET.*
- **[Low] `days` query param unbounded** (`api/analytics` GET) → `?days=99999` full-table scans. *Fix: clamp `[1,365]`.* No `.env.example` documenting required secrets.

**Verified fine (no action):** SSRF in `/api/pcpartpicker` is blocked (`lib/pcpartpicker.ts:348-359`, exact-host + suffix + path allowlist); all 6 `dangerouslySetInnerHTML` are JSON-LD `JSON.stringify` on static/server data; `SUPABASE_SERVICE_KEY` only in server files (no `NEXT_PUBLIC` leak); Lemon Squeezy webhook uses HMAC-SHA256 + constant-time compare; MDX loader genuinely try/catches per file.

**Ideas:** (1) Gate `/api/analyze` (highest value — stops sub drain + prompt injection). (2) Give the autopilot dedupe + canonical guard. (3) Canonicalize the 3 GPU-bottleneck posts. (4) **Wire the real Amazon Associates tag — `src/lib/affiliate.ts:11` still ships placeholder `bottleneck20-20`, so every affiliate click across the whole site currently earns $0.** Pure lost revenue on an affiliate-model site.

---

## wishlist-tracker  (standalone repo — personal price-watch, Next.js + Supabase)

**Maturity:** Real, working, deploy-pending. `npm run build` passes (TS clean per STATUS). Clean env hygiene: no `NEXT_PUBLIC` secret, no tracked `.env`, no TODOs. Server-only Supabase client (`src/lib/supabase/server.ts`), Discord webhook + Supabase key both read from non-`NEXT_PUBLIC` vars.

- **[Med] SSRF in the price fetcher.** `src/lib/price-fetch.ts:130` `fetchPrice(url)` fetches any user-supplied URL with `redirect:"follow"` and no host allowlist / private-IP block; the quick-add endpoint accepts arbitrary URLs. A URL like `http://169.254.169.254/...` or `http://localhost:...` gets fetched server-side. Blast radius is limited (single-user, bearer-gated, response body isn't returned — only price is extracted) but SSRF-to-internal is real. *Fix: block RFC1918 + link-local + localhost, require `http(s)`, cap redirects.*
- **[Med] Fail-open auth on both API routes.** `src/app/api/quick-add/route.ts:20-26` and `src/app/api/refresh/route.ts:28-34` enforce the bearer token **only if** `process.env.CRON_SECRET` is set; if it's unset, the endpoints are fully open. *Fix: reject (401/500) when `CRON_SECRET` is missing rather than skipping the check.*
- **[Low] RLS permissive `anon` ALL policy** on `wishlist.items` / `price_history` (`FOR ALL USING(true) WITH CHECK(true)` granted to `anon`). **Already documented and gated by the owner** (STATUS "Security note" + `docs/rls-hardening.proposed.sql`); low blast radius (single-user, non-PII, server-only key today) but same shape as the ArmyofClaws exposure. *Fix: apply the proposed migration (restrict writes to `service_role`).*

**Ideas:** (1) Apply `docs/rls-hardening.proposed.sql`. (2) Unit-test the price-fetch extractor (JSON-LD + OG) with fixture HTML — it's the moat and currently untested. (3) Ship inline item-edit (STATUS flags it TBD). (4) Complete the pending Vercel deploy.

---

## agent-os  (code on master — self-improving agent OS)

**Maturity:** Genuinely strong Phase-1 prototype (~405 LOC core + tests), audited via `git show master:` (on-disk parked tree has docs only). STATUS is exemplary — honest scope discipline, and a **real cross-session recall proof** (`tests/test_phase1_proof.py`, gated `-m e2e`, two separate `Agent` instances, decoy projects seeded so a "guess project_id=1" shortcut can't pass, asserts actual tool-call args). 10 offline unit tests pass by default. SQL is fully parameterized (no injection).

- **[Med, design] Every tool call is stored as a raw-text fact** — `graph.py` persist node writes `"Tool call: name(args) => result"` via `memory.write_fact`. Over many sessions this bloats the facts table and degrades keyword retrieval with noise. *Fix: dedupe/summarize, or separate a structured tool-log from recallable facts.*
- **[Low] Dead ternary** — `src/agent_os/agent.py:39`: `str(last.content) if isinstance(last, AIMessage) else str(last.content)` — both branches identical. *Fix: collapse, or handle non-AIMessage distinctly if that was the intent.*
- **[Low] `retrieve_facts` loads ALL of a user's facts each turn then scores in Python** (`memory/store.py`) — O(n) per turn, unbounded growth. Fine at Phase 1. *Fix: SQL prefilter / FTS; cap the table.*
- **[Low] No index on `facts(user_id)` / `episodes(user_id)`** — full scan every retrieve. *Fix: `CREATE INDEX`.*

**Ideas:** (1) Add the `user_id` indexes. (2) Add a forgetting/dedupe pass before Phase 2. (3) Measure retrieval on a recall test set (STATUS already plans this — hold the line, don't add embeddings early). This project's scope discipline is a model for the other three megaprojects.

---

## operator-suite / clipforge — GambaTime Daily lane

**Maturity:** Substantial recent lane (`clipforge/IDEAS/gambatime/`, 80 files, a real discover→verify→produce→veto→pump→learn pipeline). Audited on disk (parked-tree caveat above).

- **[Info/blocker, not a code bug] GambaTime go-live is blocked on a missing `DISCORD_WEBHOOK_URL`** — consistent with the code: `pump.py` **fail-closes correctly** (`gate is None → hold, warn once, never post`; lines ~90-105). This is a config gap, not a defect.
- **Verified clean:** no hardcoded secrets (YT tokens read from `~/.operator/secrets/clipforge_yt_token.json`, webhook via `clipforge.notify`), no TODO/FIXME in the gambatime Python, the veto gate honors delay/veto/unknown states properly.
- **[Low] The parked-branch divergence (see meta-finding) is the real risk here** — the runtime clone (not this dev clone) is what actually runs; on-disk audit is not authoritative for what's live.

---

## Checked & excluded

- **ledger** — doc-only scaffold (4 files: AGENTS.md, README, STATUS, .gitignore; first commit 2026-06-29). No code yet despite memory calling it "his biggest build." Nothing to audit beyond the integration-contract stub.
- **deal-brain** — **out of window.** Last commit 2026-06-02 (~4 weeks ago); zero commits in the 14-day window. Not recent work.

---

## Portfolio-wide — top 5 things worth doing next

1. **Rotate the two live API keys sitting in plaintext `.env` files, then decide the operator-suite parked-branch fate.** `pc-bottleneck-analyzer/.env.local` (Anthropic key) and `research-agent-platform/.env` (Anthropic + Firecrawl) are both gitignored and never committed — so this is precautionary, not a breach — but they've been plaintext on disk and one is unreferenced by code. Same session: resolve the 123 stranded commits on the parked branch (rebase/merge or confirm disposable). Highest priority because it's cheap and closes real exposure + a portfolio-hygiene risk.
2. **Gate `pc-bottleneck-analyzer`'s public API routes (`/api/analyze`, `/api/analytics` GET).** These are the only findings that let a stranger spend your Claude subscription / real $ and prompt-inject your authenticated session, on a **live** site. ~15–30 lines (reuse the `STATS_PASSWORD` cookie + an `ip_hash` rate limit).
3. **Fix the llm-benchmark-suite duplicate-ID calibration bug + add its regression test.** The judge-vs-human correlation — the entire Phase-2 done-gate — is currently computed on corrupted pairings (`calibration.py:266` + `make_grading_sheet.py:32-36`). Any decision made off that number would be wrong. Structural fix: namespace pooled IDs by source.
4. **Add citation-range validation to research-agent-platform** (`graph/nodes.py` post-synthesis: assert every `[n]` ≤ source count). A "grounded, cited" research tool that can silently ship a hallucinated citation has no moat; this is a few lines and the e2e test should assert it too.
5. **Close swe-agent-platform's two finalize gaps (base-branch detection + branch-not-default assertion + non-silent failure) before the gated Phase-2 measurement run.** A broken finalize would corrupt the `multi`-arm solved-count and invalidate the very comparison the project exists to produce. ~20 lines concentrated in `_finalize_node` + `create_pr`.

**Cross-cutting theme:** the four agentic-megaprojects are impressively real for their age, but three of four have a *correctness-of-the-core-metric* gap (benchmark scoring, research citations, agent finalize) and thin/absent tests exactly where the product's trust lives. agent-os is the exception and the template — measure the core claim, don't assume it.
