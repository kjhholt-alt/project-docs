# MEMORY OS — orient in seconds, consolidate in sleep

*Design doc · 2026-07-01 · architect session `upbeat-jackson-e44168`*
*Status: **PROPOSED** — safe pieces built in the same sprint; destructive pieces staged behind an approval gate.*

---

## 0. TL;DR

Two pains, one system:

1. **Cold start** — new sessions burn ~10 minutes re-deriving "where am I, what is this
   project, what's around it." Fix: a nightly-prebuilt **orient snapshot** that the
   existing SessionStart hook prints in <1 second, plus a location-aware umbrella
   router and a repaired umbrella root. No session ever crawls the disk to orient.

2. **No consolidation** — 314 memory files, append-only, never digested. Fix: **REM**
   (Reflect · Evaluate · Merge), a scheduled dream-state pipeline that mines new facts
   from transcripts, integrates them, resolves contradictions, and proposes
   prune/dedup — as a **staged plan you approve** before a single byte is deleted.
   The memory corpus becomes a git repo first, so every change is reversible forever.

Everything reuses what exists: Atlas (retrieval + graph + usage log), `build_index.py`
(nightly walker), `session_context.py` (hook surfacing), the runtime-clone launcher
pattern, and the Discord-gate approval culture. Nothing is rebuilt from scratch.

```
                        ┌──────────────────────────────────────────────┐
                        │                 SUBSTRATE                    │
                        │  memory/*.md (git-versioned)   MEMORY.md     │
                        │  Atlas db (rebuildable cache)  PROJECT_INDEX │
                        └──────────┬───────────────────────┬───────────┘
              read path (fast)     │                       │    write path (careful)
        ┌──────────────────────────▼──────────┐   ┌────────▼─────────────────────────┐
        │            ORIENT PLANE             │   │            REM PLANE             │
        │  every session start, <5s, free     │   │  scheduled, offline, gated       │
        │                                     │   │                                  │
        │  nightly: build_index.py            │   │  0 doctor   (deterministic)      │
        │    → PROJECT_INDEX.md               │   │  1 mine     (transcripts)        │
        │    → AGENT_CONTEXT.md               │   │  2 integrate(atlas_recall)       │
        │    → orient.json        ← NEW       │   │  3 debug    (contradictions)     │
        │  hook: session_context.py v2        │   │  4 prune    (stale rules)        │
        │    → [orient] block for cwd ← NEW   │   │  5 dedup    (overlaps)           │
        │  umbrella CLAUDE.md = router only   │   │  6 reindex  (MEMORY.md+Atlas)    │
        │    (location-aware)     ← FIXED     │   │  ──→ PLAN (staged) ──→ approval  │
        └─────────────────────────────────────┘   │  ──→ rem_apply (git commit)      │
                                                  └──────────────────────────────────┘
```

---

## 1. What exists today (audited 2026-07-01)

| Piece | State | Verdict |
|---|---|---|
| Memory corpus (`~/.claude/projects/C--Users-Kruz-Desktop-Projects/memory/`) | 314 files, 1.2 MB. Types: 176 project / 96 feedback / 33 reference / 4 user / 5 untyped | Healthy format, **zero versioning** (not a git repo), drifting index |
| `MEMORY.md` hand-curated index | ~100 files get a mention (often as prefix-stripped shorthand); **~200 files have no pointer**; dozens of shorthand slugs don't resolve to a file 1:1 | Curation is good, drift is real |
| `[[wikilink]]` graph | 572 links, 26 dangling targets, ~70 orphans | Alive but unhealed |
| Near-duplicates | 66 candidate pairs (hermes sprint trio, `verify_deploy`/`verify_push`, megasprint v1/v2, …) | Real merge fodder |
| **Atlas** (`atlas/`, installed at `~/.atlas/bin`) | Live: FTS5 + bge vectors + wikilink graph, `atlas_recall`/`atlas_freshness` MCP connected, nightly `AtlasIngestNightly` 04:30, usage log, review-gated `atlas note`, read-only Librarian | **The retrieval + dedup engine. Build on it, never beside it** |
| `tools/build_index.py` (operator-suite) | Walks every project nightly → `PROJECT_INDEX.md` + `AGENT_CONTEXT.md` + STATUS-rot check | The nightly walker — extend, don't duplicate |
| `PortfolioIndexNightly` 03:40 | Healthy (exit 0). Static launcher self-heals a runtime clone of operator-suite master and runs `tools/nightly_index_job.ps1` | The "missing" builder was never missing — see next row |
| Umbrella root (`Desktop/Projects`) | **Parked on `chore/magnum-opus-phase-0-1-docs`**: 5 commits ahead of master, 304 behind. `tools/build_index.py` exists on master but not on this branch → "path doesn't resolve" | Root repair required (§4.1) |
| `session_context.py` SessionStart hook | Prints 3 `[portfolio]` lines (<2s, fail-silent), runtime copy refreshed nightly | The surfacing rail — extend with `[orient]` |
| Umbrella `CLAUDE.md` | Says "you are at the umbrella" even when a subproject CLAUDE.md is loaded alongside it (parent-dir CLAUDE.md files always load) → sessions re-verify location → disk crawl | **Cold-start bug confirmed.** Rewrite location-aware |
| Session transcripts | 237 JSONL / 28 MB in the last 7 days under `~/.claude/projects/C--Users-Kruz-Desktop-Projects/` | The ore REM mines |
| Atlas ingest semantics | Recursive walk, every `.md`, no default skips; DB is a **rebuildable cache** with no tombstoning | Consolidation must skip `_attic/` and rebuild the DB after destructive ops |

---

## 2. Root causes, precisely

**Cold start** is three stacked failures:
1. The umbrella router asserts "you are at the umbrella, redirect" even when the
   session is already inside a project (both CLAUDE.md files load). The model obeys
   the louder instruction and re-verifies its location — by crawling.
2. The promised orientation artifacts point at a path (`tools/build_index.py`) that
   isn't on disk because the root checkout is parked on a stale branch. First tool
   call fails → trust in the whole orientation layer collapses → crawling.
3. There is no per-project "you are here" card. `PROJECT_INDEX.md` answers "where is
   X" but not "what is *here*, how fresh is it, what should I read first."

**No consolidation** is one missing organ: everything writes memory, nothing digests
it. The write protocol is fine; there is no scheduled process with the *mandate* to
merge, reverse, retire, and re-point — and no safety substrate (versioning) that would
make such a process safe to run.

---

## 3. File taxonomy — who owns what, what loads when

One table, the whole hierarchy. "Owner" = who is allowed to mutate it.

| File | Role | Owner / mutation path | Loaded when |
|---|---|---|---|
| `~/.claude/CLAUDE.md` | Rules of engagement: environment, rails, how-Kruz-works | Kruz + Claude, deliberate edits only | Every session (global) |
| `Projects/CLAUDE.md` (umbrella) | **Router only.** Location-aware: defers to any deeper CLAUDE.md | Repo (operator-suite), via PR | Root sessions AND every subproject session (parent walk) — hence must be self-deferring |
| `<project>/CLAUDE.md` | Project conventions, commands, stack | The project, via its repo | Sessions inside that project |
| `<project>/STATUS.md` | Living state: working / broken / next | End-of-session rule (unchanged) | On demand; freshness policed by nightly rot check |
| `MEMORY.md` | **Hot index** — one line per memory worth ambient recall | Sessions append pointers; **REM heals** (dead lines out, missing pointers in, ghosts fixed). Never bulk-regenerated — curation is the value | Every session (auto) |
| `memory/*.md` | **Cold store** — one durable fact per file, frontmatter + wikilinks (format unchanged; Atlas depends on it) | Sessions write per §6; REM merges/retires via approved plans only | Via grep / `atlas_recall` |
| `memory/_attic/` | Soft-deleted memories (full file, `status: atticked` + reason added) | REM apply only | Never (excluded from index, MEMORY.md, and Atlas ingest) |
| `~/.operator/rem/` | REM state: watermark, plans, run reports | REM pipeline | On demand (plan review) |
| `PROJECT_INDEX.md`, `AGENT_CONTEXT.md`, `orient.json` | Generated catalog / Codex digest / orient cards | `build_index.py` only — never hand-edit | Index: greped on demand. orient.json: read by the hook |
| Atlas DB (`~/.atlas/atlas.db`) | Rebuildable retrieval cache | `atlas ingest` (nightly + post-apply rebuild) | Via MCP `atlas_recall` |
| `Projects/memory/` (dated 2026-04 files) | **Legacy** pre-protocol memory dir at the Projects root | — | Proposed: attic in a REM plan; not touched now |

Ambient context budget stays flat: global CLAUDE.md + umbrella router + project
CLAUDE.md + MEMORY.md + ~8 hook lines. Everything else is pull, not push.

---

## 4. Orient plane — the cold-start fix

### 4.1 Root repair (one-time, prerequisite)
Merge the parked branch's 5 real commits into master via PR (conflicts resolved
preferring master for generated files), then move the root checkout to master and
pull. Result: `tools/` exists on disk again, the umbrella CLAUDE.md on disk is the
fixed one, and the nightly "PARKED" alert goes quiet. All work is committed before
any branch move — nothing can be lost, the old branch remains.

### 4.2 `orient.json` (new emit from the existing walker)
`build_index.py` already visits every project nightly; it additionally writes
`~/.operator/portfolio_index/orient.json`:

```json
{ "generated": "2026-07-02 03:40",
  "projects": { "deal-brain": {
      "status_title": "STATUS — DealBrain", "status_date": "2026-06-29",
      "newest_doc": "2026-06-30", "docs": 22,
      "what": "first heading of CLAUDE.md or README",
      "git_last_commit": "2026-06-29", "protected": false } } }
```

`protected` is hardcoded for `municipal-crm` and `BarrelHouseCRM`. Cost: ~0 (data
already in hand during the walk).

### 4.3 `session_context.py` v2 (same hook, +2 lines of output)
The hook already runs on every SessionStart. v2 reads the hook's stdin JSON (`cwd`),
resolves which project the session is in, and prints from `orient.json` — no disk
walk, no git calls beyond the existing ones, still fail-silent, still <2s:

```
[orient] you are IN deal-brain -- "DealBrain — AI M&A reports" | STATUS.md 2026-06-29 | newest doc 2026-06-30 | last commit 2026-06-29
[orient] read next: deal-brain/STATUS.md + deal-brain/CLAUDE.md. Do NOT crawl to orient; where-is-X => grep PROJECT_INDEX.md
```

At the umbrella root it prints the 5 most recently active projects instead. In a
worktree it says so and names the backing project. **This is the 5-second contract:**
by the time the model reads its first user message it already knows where it is, what
the project is, how fresh it is, and what to read next — from ambient context alone.

### 4.4 Umbrella `CLAUDE.md` rewrite
New first section, before anything else:

> **Location check (read first).** If a more specific `CLAUDE.md` (a subproject's) is
> also loaded, or an `[orient] you are IN <project>` line appears in session context,
> **you are in that project — not at the umbrella. Skip this file.** It is a router
> for root-level sessions only. Never crawl the disk to verify where you are.

Plus: stale `tools/build_index.py` claims corrected to the real chain (root copy →
runtime fallback), and the router table kept as-is (it works).

---

## 5. REM plane — the dream state

**REM = Reflect · Evaluate · Merge.** A weekly offline pass (Sun 05:00, after Atlas's
04:30 ingest) plus a nightly read-only health line. Runs headless via the same static
launcher pattern as the index job. Two hard rules: **every destructive step ships as a
staged plan first**, and **the corpus is a git repo before REM ever runs**.

### Phase 0 — DOCTOR (deterministic, no LLM)
`tools/memory_doctor.py`: frontmatter health, index drift (shorthand-aware), dangling
wikilinks, orphans, staleness buckets, dupe candidates (name-token + description
similarity). Pure read-only, ASCII, JSON + human report. Also runs nightly inside
`nightly_index_job.ps1`, feeding one summary line into the existing alert flow —
memory health becomes visible the same way STATUS rot already is.

### Phase 1 — MINE (transcripts → candidate facts)
`tools/rem_mine.py` pre-filters transcripts newer than the watermark
(`~/.operator/rem/state.json`): extracts user messages (where corrections and
decisions live) with session provenance, drops trivia, caps size. The REM session
then *judges* the bounded digest: durable fact? correction? decision? new project?
Each survivor becomes a candidate with a provenance quote. Mining backstops the
session-time write rules — what sessions forget to save, the dream recovers.

### Phase 2 — INTEGRATE (candidate → new file / update / discard)
For each candidate: `atlas_recall` (semantic, not just grep). Covered by an existing
memory → discard. Extends one → propose an edit to that file. Genuinely new →
propose a new file (type prefix per §6) + MEMORY.md pointer + wikilinks.

### Phase 3 — DEBUG (contradiction resolution)
Candidates and recent facts that *conflict* with stored memory (project revived vs
dead, rule reversed, path moved) → propose a **rewrite that reverses the old fact**
(the `rethink_as_learning` rule: reverse, don't layer), citing provenance. Never
silently keep both.

### Phase 4 — PRUNE (retire stale judgment)
Nominees, all three signals considered together: (a) references a project in the
archived/dead list, (b) zero `atlas usage.log` recall hits + orphan + unindexed,
(c) superseded by a newer rule. Proposal is always **move to `_attic/` with a reason
line** — never delete. Protected rails (the "CRITICAL — never violate" block) are
categorically exempt from pruning.

### Phase 5 — DEDUP (merge overlaps)
Doctor nominates; the REM session judges with both files + Atlas context. Parent/child
families (`war_table` vs `war_table_arena`) are *links*, not dupes — merge only true
overlapping claims. Merge = one file absorbs (union of facts, newest wins), the other
goes to `_attic/`, every inbound wikilink and MEMORY.md pointer is rewritten.

### Phase 6 — REINDEX (heal, then rebuild caches)
MEMORY.md surgical healing (dead lines out, missing pointers appended to the right
section, ghost slugs fixed — voice preserved, never regenerated). Dangling wikilinks
fixed or dropped. Then: wipe + full `atlas ingest` (the DB is a rebuildable cache and
has no tombstones — rebuild is the correct ghost-killer), then `build_index.py`.

### The gate
Phases 0–5 write **only** `~/.operator/rem/PLAN-<date>.md` (human) +
`plan-<date>.json` (machine: op list with type / target / before / after / reason /
provenance). The plan summary posts to Discord. **Nothing applies until Kruz says
apply.** Then `tools/rem_apply.py`: re-verifies every target file's hash against the
plan (drifted → op skipped, reported), applies, `git commit` in the memory repo with
the plan ID, runs Phase 6. Rollback = `git revert`. Purely **additive** ops (new
memory from mining, pointer appends, dangling-link fixes) may auto-apply from v2
onward once trust is earned; v1 gates everything.

---

## 6. Write rules (session-time — protocol unchanged, triggers sharpened)

Format, frontmatter, one-fact-per-file, wikilinks, MEMORY.md pointer: **unchanged**
(Atlas and the global protocol both depend on it). What this design adds is *when*:

**Write a memory when:** Kruz corrects how you work (→ `feedback_`, with Why + How to
apply); a durable decision or pivot lands (→ `project_`); a new project/lane is born
(→ `project_`); a gotcha costs you >10 minutes and will recur (→ `feedback_` or
`reference_`); an external resource/key/dashboard gets located (→ `reference_`, names
only, never secrets). **Don't write:** anything derivable from the repo, session-local
state, or gated items as next-steps. Update-don't-duplicate: check MEMORY.md /
`atlas_recall` first; wrong memories are corrected in place, not appended around.

Sessions stay writers; REM is the janitor. Neither replaces the other.

---

## 7. Safety model (the part that never bends)

1. **Git substrate first.** `git init` + baseline commit in the memory dir before any
   pipeline exists. Local repo only — **never pushed anywhere** (personal context).
   Every REM apply is one commit tagged with its plan ID. `git revert` is the undo.
2. **Stage → approve → apply.** No REM phase mutates the corpus. Only `rem_apply.py`
   does, only on an approved plan, only ops whose target-file hashes still match.
3. **Attic, not delete.** Retirement = move + annotate. The file, its history, and its
   reason for retirement all survive. Attic is invisible to recall but present on disk.
4. **Protected memories.** The CRITICAL-rails block and `user_*` files are exempt from
   prune/dedup ops; `rem_apply.py` refuses ops targeting them regardless of plan.
5. **Caches are rebuilt, never trusted across mutations.** Post-apply: Atlas wipe +
   re-ingest, index rebuild. No ghost recall.
6. **Fail-silent surfacing.** Hook changes keep the try/except-everything, <2s,
   ASCII-safe discipline — a broken orient line must never block a session.
7. **Honest reporting.** Every REM run writes a report (proposed vs applied vs skipped
   + why) and posts it; skipped-on-drift is a headline, not a footnote.

---

## 8. Runtime layout & schedule

| When | Task | What runs |
|---|---|---|
| 03:40 daily | `PortfolioIndexNightly` (existing) | index + `orient.json` + STATUS-rot + **doctor health line** → alerts |
| 04:30 daily | `AtlasIngestNightly` (existing) | `atlas ingest --portfolio` |
| 05:00 Sun | `RemWeeklyDream` (new) | static launcher → runtime clone → headless `/rem` → staged PLAN → Discord |
| Every session | SessionStart hooks (existing) | `[portfolio]` + `[orient]` lines |
| On approval | manual / any session | `py tools/rem_apply.py --plan <id>` |

New code lives in operator-suite `tools/` (versioned, PR'd, self-healed into the
runtime clone like everything else). Skill `/rem` installs to `~/.claude/skills/rem/`
with the tracked source in `tools/skills/rem/`.

## 9. Proposed but NOT done without a separate yes

- **Slim the global CLAUDE.md project directory.** It duplicates what
  `PROJECT_INDEX.md`/`orient.json` now do better and burns tokens every session.
  Proposal: keep rails + pointers, retire the big tables. Needs your call.
- **Attic the legacy `Projects/memory/` dated files** (April-era, pre-protocol).
- **v2 auto-apply for additive-only REM ops** after a few clean gated cycles.
- **`atlas similar <doc>`** (embedding-space dupe nominator) — nice-to-have; the
  doctor's lexical pass + REM judgment covers v1.

## 10. Failure modes → defenses

| Failure | Defense |
|---|---|
| Dream hallucinates a merge and destroys nuance | staged plan + your approval + git revert + attic copy |
| Two sessions write while a plan is pending | hash preconditions → op skips, reports, next dream re-proposes |
| Orient data goes stale and lies (deadliest: stale trust) | every `[orient]` line carries its generation date; nightly regeneration; fail-silent absence beats confident wrongness |
| REM run dies mid-way | phases only write plan files; apply is atomic-ish (per-op) + git status always shows truth |
| Index/hook scripts drift from repo | existing self-heal: nightly job refreshes runtime copies from master |
| Atlas recalls atticked/deleted memories | `_attic` ingest skip + post-apply DB rebuild |
