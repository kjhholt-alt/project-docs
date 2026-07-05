# Code Review: WezTerm Dashboard

Scope:
- `C:\Users\Kruz\.wezterm.lua`
- `C:\Users\Kruz\Desktop\Projects\dashboard.py`

Date: 2026-02-21

## Findings (Highest Severity First)

1. Silent failure hides data errors and makes “wrong P&L/price” undetectable.
   - All API calls in `C:\Users\Kruz\Desktop\Projects\dashboard.py` swallow exceptions and return empty data, which renders “No open positions” or stale values with no indication of failure. This makes regressions look like “data is wrong” instead of “data is unavailable”.
   - Impact: Misleading or empty P&L/price without any signal. Hard to debug.
   - Fix: Track `last_success_at`, show a warning banner when data is stale, and include a small error indicator for failed requests (e.g., “PM API: error”).

2. Data dependencies are brittle to API schema drift and token/market mismatches.
   - The Polymarket data API has changed field names over time. Even with the recent multi-field fallbacks, there is no schema validation or sanity checking. If it shifts again, values silently go to zero.
   - Impact: Incorrect P&L/price display without visible error.
   - Fix: Implement a schema adapter with explicit field expectations and a fallback path that logs or displays a warning when a required field is missing.

3. “Bet” column is ambiguous and can be misleading.
   - Current logic uses recent trades window “BUY” cost if present, otherwise total cost basis. This mixes “recent spend” and “position cost” and ignores sells in the window.
   - Impact: “Bet” looks wrong when there are sells or when the window excludes earlier buys.
   - Fix: Decide on a single definition. Either “net exposure” (buys - sells in window) or “position cost basis” (total cost). Label accordingly.

4. Dashboard launch path is fragile due to `py` reliance.
   - WezTerm runs `py "C:/Users/Kruz/Desktop/Projects/dashboard.py"`. The Python launcher is sometimes blocked on this machine (observed error), which will make the dashboard pane fail silently.
   - Impact: Dashboard doesn’t start or intermittently fails.
   - Fix: Use `python` or an explicit absolute path to a known Python install, and add a health check in WezTerm that prints a clear error if the dashboard process exits.

5. Secrets are hard-coded in the dashboard file.
   - The Supabase anon key is embedded directly in `C:\Users\Kruz\Desktop\Projects\dashboard.py`.
   - Impact: Easy to leak if the file is shared or pushed. Even anon keys should be treated as configuration.
   - Fix: Move to environment variables and provide a `.env.example`.

## Recommendations (Lower Risk / Quality)

1. Add simple caching to smooth out intermittent network issues.
   - Cache last valid Polymarket response to a local JSON file. If the API fails, display cached values with “stale” label.

2. Use a `requests.Session` and basic retry for network reliability.
   - Retries on transient failures reduce “empty” panes.

3. Add a minimal “status row”.
   - Show last refresh timestamps for wallet and polymarket data (e.g., `PM: 12:04:12 OK` / `PM: error`).

4. Add a fast local health probe.
   - On startup, run a quick “ping” to the Polymarket data API and render a top-right warning if unreachable.

## Tests / Validation Gaps

- No automated tests or scripted validation for the dashboard data adapter and P&L calculations.
- Suggestion: Add a small `tests/` script with sample API payload fixtures and run it from the terminal on demand.

