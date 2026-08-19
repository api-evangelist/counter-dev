---
name: counter-dev-install-tracking
description: Install the Counter tracking snippet on a website and verify that visits are being recorded. Use when asked to add privacy-friendly analytics to a site, set up counter.dev, or debug why a Counter-tracked site is showing no data.
api: counter-dev:counter-dev-tracking-api
operations:
  - track
  - trackpage
generated: '2026-08-13'
method: generated
source: openapi/counter-dev-tracking-api-openapi.yml, components/counter-dev-components.yml, https://counter.dev/help/integration.html
---

# Install Counter tracking

Counter's collect surface is two operations, `track` and `trackpage`, and in
practice you never call them directly — a ~1.1 KB browser snippet does. Your job
is to place the snippet correctly and verify it is firing.

## Steps

1. **Get the account's `data-id`.** It is the per-account UUID shown in the
   Counter dashboard after sign-up. It is a public identifier, not a secret — it
   is deliberately a UUID rather than the username so a site can publish it
   without exposing the account name. You cannot generate or guess it; the user
   must copy it from https://counter.dev/app.

2. **Place the snippet immediately before the closing `</head>` tag.**

   ```html
   <script src="https://cdn.counter.dev/script.js" data-id="YOUR-UUID" data-utcoffset="0"></script>
   ```

   | Attribute | Required | Purpose |
   |---|---|---|
   | `data-id` | yes | The account UUID. |
   | `data-utcoffset` | no | The account's UTC offset, so visits land in the right local day and hour. |
   | `data-server` | no | Overrides the collect host. Defaults to `https://t.counter.dev`. **This is the hook for a self-hosted instance** — point it at your own deployment. |

   For WordPress, Shopify, Squarespace, Wix, Weebly or Backdrop, Counter
   documents where the `<head>` lives on each platform at
   https://counter.dev/help/ — there is no plugin or app for any of them, only
   manual snippet placement.

3. **Know what the snippet does**, because it explains most "no data" reports:

   - `track` fires via `fetch()` **4.5 seconds after load**, and only when
     `sessionStorage` has no `_swa` flag *and* the referrer is not same-origin.
     That is how Counter counts unique visits without cookies — a visitor
     clicking around the site is counted once, not once per page.
   - `trackpage` fires via `navigator.sendBeacon()` on **every** page load,
     recording a per-path counter.
   - The snippet returns immediately, doing nothing, if a `doNotTrack` key exists
     in `sessionStorage` or `localStorage`. This is a real per-visitor opt-out a
     site can offer.

4. **Verify.** Load the page, wait more than five seconds, then read the account
   with the `counter-dev-read-site-stats` skill and look for the site host as a
   top-level key. Allow for the day/hour bucketing to follow `data-utcoffset`,
   not the visitor's clock.

## Debugging "no data is showing up"

Work down this list — every item is a real, deliberate drop in Counter's collect
handler, not a bug:

- **Testing from `localhost` or `127.0.0.1`.** Silently dropped. Test from a real
  origin.
- **Missing `Origin` header.** Both collect endpoints reject a request without
  one — the site id is *derived* from `Origin`, so it is load-bearing, not just a
  security check. A bare `curl` to `/track` returns 400
  `Origin header can not be empty, not set or "null"`; the same call with
  `-H "Origin: https://example.com"` returns 200 `OK`.
- **Headless or bot user agent.** Dropped for detected bots and for user agents
  containing `HeadlessChrome/`, `PetalBot;` or `AdsBot`. Your automated check may
  be invisible for exactly this reason.
- **Google Translate proxy.** Origins ending `.translate.goog` return 400
  `Ignoring due origin`, so translated pages are not double-counted.
- **Returning within the same tab.** The `_swa` sessionStorage flag suppresses
  the visit call. Open a fresh tab or clear session storage.
- **Fewer than 4.5 seconds on the page.** The visit call never fires. Only the
  `trackpage` beacon does.
- **A tracking blocker.** Counter's own help page lists this first.
- **Wrong `data-id`.** `POST /trackpage` with an unknown or empty `id` returns
  **500** with the plain-text body `No such user with uuid: ` — a 500 here means
  a bad id, not a Counter outage.

## Cautions

- **Neither operation is idempotent.** `track` is a `GET` that increments
  counters, so a retry, a prefetch, or an aggressive link-preloader inflates the
  numbers. Never retry a collect call, and never call `track` yourself to "test"
  a live account — you are writing real data.
- **Do not point `track` at someone else's account id**, including Counter's
  public demo account (`user=counter`). It writes to their real counters.
- **The CDN URL is unpinned.** `https://cdn.counter.dev/script.js` carries no
  version, so every site takes whatever is currently served. If that is
  unacceptable for your supply-chain posture, vendor the file yourself — it is
  ~1.1 KB of plain JavaScript, AGPL-3.0, published at
  https://github.com/ihucos/x-cdn.counter.dev.
