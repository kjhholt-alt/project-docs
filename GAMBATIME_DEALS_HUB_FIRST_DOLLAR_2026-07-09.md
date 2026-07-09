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
- A public GambaTime YouTube About link to the hub with an explicit affiliate disclosure.
- A backup of the pre-change channel description at `C:\Users\Kruz\.operator\data\gambatime_channel_description_backup_2026-07-09.json`.

## Funnel

`GambaTime viewer -> buildkit.store/go/deals -> /deals -> /go/<game> -> Instant Gaming -> commission`

The owned click is the leading indicator. Instant Gaming's commission ledger remains the source of truth for revenue.

## Measurement Contract

Review once after seven full days, then weekly. Do not redesign the page from impressions alone.

| Signal | Where | Decision |
|---|---|---|
| `/deals` pageviews | BuildKit collector | Confirms the channel link is being used |
| `/go/click/<slug>` events | BuildKit collector | Ranks game interest and measures outbound intent |
| Commission / sale | Instant Gaming dashboard | Confirms first dollar and actual conversion |

### Seven-Day Actions

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

- `npm run test:deals`: 4 tests passed.
- `npx tsc --noEmit`: passed.
- `npm run build`: passed.
- Production `https://buildkit.store/deals`: HTTP 200, canonical present, disclosure present, eight offer links present.
- Production `https://buildkit.store/go/deals`: HTTP 302 to the owned hub.
- Production game redirect: HTTP 302 to Instant Gaming with `igr=freegames4u`.
- Official Steam image assets: all nine requested images returned HTTP 200.
- BuildKit collector synthetic event: HTTP 200 with `{\"ok\":true}`.
- Public GambaTime About page: HTTP 200 and contains both the hub URL and affiliate disclosure.

## Next Attention

The highest-leverage next move is distribution, not another product build. After the seven-day baseline, stamp the hub entry point onto the five GambaTime uploads with the strongest purchase intent, preserving per-video identifiers where available. The first iteration should be driven by clicked games and network commissions, not a larger catalog.
