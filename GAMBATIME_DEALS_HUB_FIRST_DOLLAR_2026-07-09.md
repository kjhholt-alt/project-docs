# GambaTime Deals Hub: First-Dollar Launch

**Shipped:** 2026-07-09  
**Live surface:** https://buildkit.store/deals  
**Channel entry point:** https://buildkit.store/go/deals  
**Affiliate network:** Instant Gaming (`igr=freegames4u`)

## Decision

Build an owned game-discovery page under GambaTime and route the channel's existing audience through it before sending buyers to the affiliate storefront.

This was chosen over a new product or the existing paid PC optimizer because it had the shortest complete path to a legitimate first dollar:

1. GambaTime already has public gaming traffic.
2. The Instant Gaming affiliate account and approved tracking tag already exist.
3. The portfolio domain and production deployment already exist.
4. No payment processor, customer support promise, inventory purchase, or new account approval is required.
5. The owned page makes interest measurable and lets the offer mix improve without editing hundreds of videos.

The paid PC optimizer was deliberately not activated. Its public Lemon Squeezy checkout is still in test mode, and its production order table and webhook secret are absent. Enabling that gate would create a broken purchase experience, not revenue.

## What Shipped

- A mobile-first `/deals` page with eight paid PC games, official Steam artwork, search, and category controls.
- A central offer catalog so displayed titles and affiliate destinations are tested together.
- Outbound `/go/<slug>` redirects that preserve the existing `freegames4u` tag.
- Fail-open server-side click attribution written as `/go/click/<slug>?src=<source>` to the existing BuildKit collector.
- `/go` and `/go/deals` now land on the owned hub instead of immediately losing the visitor to a storefront.
- A GambaTime channel-description URL and explicit affiliate disclosure. This is
  not yet a formal clickable YouTube profile link.
- A BuildKit Play header, footer, and home-page bridge into the Deals Hub.
- Source-tagged hub-entry receipts for `buildkit-play-nav` and
  `buildkit-play-home`.
- A backup of the pre-change channel description at `C:\Users\Kruz\.operator\data\gambatime_channel_description_backup_2026-07-09.json`.

## Funnel

`GambaTime viewer -> buildkit.store/go/deals -> /deals -> /go/<game> -> Instant Gaming -> commission`

The owned click is the leading indicator. Instant Gaming's commission ledger remains the source of truth for revenue.

## Measurement Contract

Start the experiment only after the formal YouTube profile links are live.
Review after 14 full days, then weekly. Do not redesign the page from impressions alone.

```powershell
cd C:\Users\Kruz\Desktop\Projects\operator-scripts
py tools\deals_funnel_report.py --start-experiment
py tools\deals_funnel_report.py
```

`--start-experiment` freezes the current Redis counts, including launch probes,
so only later deltas can satisfy the 50-session and 10-outbound-click gates.
Known synthetic sources and verification slugs are excluded automatically.

`BuildKitDealsFunnelDaily` runs hidden at 09:05 America/Chicago and refreshes:

- `C:\Users\Kruz\.operator\data\deals_funnel_latest.json`
- `C:\Users\Kruz\.operator\data\deals_funnel_latest.txt`
- `C:\Users\Kruz\.operator\data\deals_funnel.log`

| Signal | Where | Decision |
|---|---|---|
| `/deals` pageviews | BuildKit collector | Confirms the channel link is being used |
| `/go/click/<slug>` events | BuildKit collector | Ranks game interest and measures outbound intent |
| Commission / sale | Instant Gaming dashboard | Confirms first dollar and actual conversion |

### Fourteen-Day Actions

- **10+ outbound clicks:** keep the lane live; move the top two clicked games first and add only closely related offers.
- **1-9 outbound clicks:** improve placement in future GambaTime descriptions and pin the hub on relevant uploads; do not add more catalog complexity yet.
- **0 outbound clicks with pageviews:** revise the offer selection and card copy.
- **0 pageviews:** distribution is the problem; add the hub link to the next five high-intent GambaTime uploads rather than rebuilding the site.
- **First commission:** record game, source, click date, sale date, and commission amount in the first-dollar ledger before expanding spend.

## Guardrails

- Never claim a specific discount or lowest price without current storefront data.
- Keep the affiliate disclosure visible before the deal grid and in public channel copy.
- Attribution must remain fail-open: analytics failure cannot block the redirect.
- Preserve the `igr=freegames4u` tag in tests.
- Remove unavailable offers rather than forwarding users to irrelevant products.
- Do not spend on paid acquisition until at least one organic commission proves the funnel.

## Verification Receipt

- `npm run test:deals`: 5 tests passed.
- `npx tsc --noEmit`: passed.
- `npm run build`: passed.
- Production `https://buildkit.store/deals`: HTTP 200, canonical present, disclosure present, eight offer links present.
- Production `https://buildkit.store/go/deals`: HTTP 302 to the owned hub.
- Production game redirect: HTTP 302 to Instant Gaming with `igr=freegames4u`.
- Official Steam image assets: all nine requested images returned HTTP 200.
- BuildKit collector synthetic event: HTTP 200 with `{\"ok\":true}`.
- Public GambaTime page: HTTP 200 and the channel description contains the hub
  URL and affiliate disclosure; formal profile links remain unset.
- BuildKit Play revenue bridge: production HTML contains the header/footer link
  and home promotion; `/go/deals?src=buildkit-play-home` preserves attribution.
- `py tools\deals_funnel_report.py`: live Redis report works; 5 focused tests pass.
- `BuildKitDealsFunnelDaily`: registered hidden for 09:05; manual task proof
  completed with `LastTaskResult: 0` and refreshed both durable receipts.
- Pre-baseline observation: 1 hub visit, 11 non-synthetic outbound receipts, and
  6 synthetic receipts excluded. These are historical observations, not proof
  results, because the formal profile-link baseline has not started.

## Next Attention

The highest-leverage next move is distribution, not another product build. Add
formal channel profile link #1 as `Game Deals` pointing to
`https://buildkit.store/go/deals?src=youtube-profile`, add profile link #2 as
`BuildKit Play`, then run `--start-experiment`. The next iteration should be
driven by post-baseline clicked games and network commissions, not a larger catalog.
