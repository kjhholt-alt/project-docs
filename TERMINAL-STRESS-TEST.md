# THE TERMINAL — Stress Test Prompt

## Instructions

Post this ENTIRE prompt to **#claude-1** after restarting WezTerm with 4 panes.
Claude 1 will act as the Planner and decompose this into subtasks for Claude 2, 3, and 4.

Watch #fleet-ops for progress updates. This tests the full pipeline:
**Plan → Delegate → Code → Review → Deploy**

---

## THE PROMPT (paste this to #claude-1)

You are PLANNER CLAUDE in The Terminal fleet. You have 3 other Claude Code agents available. Use the fleet CLI to coordinate:

```
bun run /c/Users/Kruz/Desktop/Projects/jarvis-dashboard/collector/fleet-cli.ts direct claude-2 "message"
bun run /c/Users/Kruz/Desktop/Projects/jarvis-dashboard/collector/fleet-cli.ts direct claude-3 "message"
bun run /c/Users/Kruz/Desktop/Projects/jarvis-dashboard/collector/fleet-cli.ts direct claude-4 "message"
bun run /c/Users/Kruz/Desktop/Projects/jarvis-dashboard/collector/fleet-cli.ts status --json
```

## YOUR TASK: PL Engine — Factory Comparison Report

Build a new script `scripts/factory_comparison.py` that generates a side-by-side comparison of ALL factories for leadership review. This is a real deliverable we need.

### What it should produce:

An Excel workbook (`outputs/PL2027_Factory_Comparison.xlsx`) with 4 sheets:

**Sheet 1: Side-by-Side Summary**
- One row per factory (AX01, AX02, JL01, HX01, T801)
- Columns: Factory, Name, Normal Output Hours, PL2027 Normal Total, SL2026 Normal Total, $ Variance, % Variance, CPOH, Headcount (Salary), Headcount (Wage)
- Sorted by PL2027 total descending
- Grand total row
- Conditional formatting: green if favorable variance, red if >5% unfavorable

**Sheet 2: Category Breakdown**
- Rows: each factory
- Columns: Depreciation, Utilities, Benefits, Headcount, TOTAL
- All at Normal scenario
- Stacked bar chart showing composition per factory

**Sheet 3: CPOH Trend**
- CPOH at each scenario (ST/T/N/P/SP) per factory
- Line chart with one line per factory
- Highlight which factory has highest/lowest CPOH at Normal

**Sheet 4: Data Quality**
- Per factory: config completeness %, seed data available (Y/N), last pipeline run, test count, known issues
- Traffic light formatting (green/yellow/red)

### How to decompose this:

1. **Claude 2** (CODER): Build Sheet 1 + Sheet 2 — the core data loading and summary tables. Use openpyxl with the formatting constants from org_packet.py. Read from seed_data/{factory} Aggregate Summary.csv and configs/.

2. **Claude 3** (CODER): Build Sheet 3 + Sheet 4 — CPOH calculations and data quality checks. Use the same openpyxl patterns. Read from configs/ and run validate_inputs.py logic.

3. **Claude 4** (REVIEWER): Once Claude 2 and Claude 3 push their code, review both for:
   - Correct data loading (handles missing factories gracefully)
   - Chart formatting matches org_packet.py style
   - No hardcoded values
   - Tests pass

4. **You (Claude 1)** (PLANNER + INTEGRATOR):
   - Send the subtask prompts to each agent via fleet CLI
   - Monitor progress with `fleet status` and `fleet log`
   - After Claude 4 reviews, merge branches if using worktrees, or verify the file works
   - Run: `python scripts/factory_comparison.py --all`
   - If it generates the Excel successfully, commit and push
   - Report results to #fleet-ops

### Ground rules:
- The script should work with whatever data exists (graceful degradation — if a factory has no aggregate CSV, show "NO DATA")
- Follow existing PL Engine patterns (look at src/org_packet.py for openpyxl chart/formatting patterns)
- Run `py -m pytest tests/ -v --tb=short` at the end to make sure nothing broke
- All code goes in `scripts/factory_comparison.py` (single file, ~300-400 lines)

### Start now.
Decompose this into the 3 subtasks, send them to Claude 2/3/4 via fleet CLI, then monitor and integrate.
