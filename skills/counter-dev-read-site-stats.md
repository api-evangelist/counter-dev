---
name: counter-dev-read-site-stats
description: Read aggregated web analytics for a Counter account over a date range, using a read-only share token. Use when asked how many visits a site got, which countries or browsers visitors came from, or which pages and referrers led traffic.
api: counter-dev:counter-dev-stats-api
operations:
  - query
generated: '2026-08-13'
method: generated
source: openapi/counter-dev-stats-api-openapi.yml, conventions/counter-dev-conventions.yml, errors/counter-dev-problem-types.yml
---

# Read Counter site stats

Counter's `query` operation returns aggregated visit data for one account across
a date range. It is the only operation that answers historical questions, and it
is read-only.

## Before you start

Counter has **no OAuth and no scopes**. Reads are authenticated by a read-only
token pair passed as query parameters:

```
?user=<account-id>&token=<token>
```

The account holder finds both in their Counter dashboard. There is no
header-based alternative — the token travels in the URL, so do not echo a full
request URL back to a user or write one into a log.

**Explore without a credential first.** Counter runs a public demo account.
Substituting `demo=1` for the token pair returns real aggregated data with no
authentication at all, and is the right way to learn the response shape before
asking anyone for a token.

## Steps

1. **Resolve the window.** `from` and `to` are both required and must be
   `YYYY-MM-DD`. Validate them yourself — Counter does not. A malformed date
   returns HTTP 500 with a raw Go parsing error, not a 400.

2. **Call `query`** — `GET /query` on `https://counter.dev` (or
   `https://t.counter.dev`; both hosts serve the identical endpoint set).

   ```
   GET /query?user=ACCOUNT&token=TOKEN&from=2026-08-01&to=2026-08-10
   ```

   Demo variant, no credential:

   ```
   GET /query?demo=1&from=2026-08-01&to=2026-08-10
   ```

3. **Parse as JSON even though the header says otherwise.** The response is a
   JSON document served with `Content-Type: text/plain; charset=utf-8`. This is a
   Counter defect, not an error condition. Do not dispatch on the media type.

4. **Read the shape.** The body is nested three levels: site host → dimension →
   value → count.

   ```json
   {
     "example.com": {
       "browser":  {"Firefox": 42, "Chrome": 118},
       "country":  {"us": 90, "de": 70},
       "date":     {"2026-08-02": 31},
       "device":   {"Computer": 120, "Phone": 40},
       "hour":     {"14": 12},
       "lang":     {"en": 150},
       "page":     {"/pricing": 22},
       "ref":      {"news.ycombinator.com": 61},
       "screen":   {"1920x1080": 55},
       "weekday":  {"Monday": 24},
       "platform": {"Mac": 60}
     }
   }
   ```

   Sum a dimension's values to get the total for that window. Every account's
   sites appear as separate top-level keys — filter by host if the user asked
   about one site.

## What you cannot do with this operation

- **There is no pagination.** No cursor, offset, limit or `Link` header. The only
  way to bound a response is to narrow the date range.
- **Long-tail values are missing, not paginated.** Counter trims the open-ended
  dimensions (`lang`, `ref`, `loc`, `page`) to the top 100 entries per bucket
  server-side. Never present these as exhaustive; say "top referrers", not "all
  referrers".
- **There is no per-visitor data to retrieve.** Counter stores no visitor,
  session or event records at all. If asked to identify or follow an individual
  visitor, explain that the data does not exist rather than approximating it.
- **Old short-window data is gone.** Daily Redis buckets expire after two days;
  only the daily SQL archive (which began 2022-09-19) persists. A request for a
  window before an account's oldest archived date returns nothing.

## Errors

Counter returns bare plain-text bodies with no JSON envelope and no error code.
Match on status, and treat the body as a human hint only.

| Status | Body | What it means |
|---|---|---|
| 403 | `Forbidden` | No valid session and no valid `user`+`token`. **A wrong token returns exactly this too** — you cannot distinguish "missing" from "invalid". Ask the user to re-copy the token from the dashboard. |
| 500 | `parsing time "..." as "2006-01-02": ...` | Malformed `from` or `to`. Your input bug, reported as a server error. Fix the date and retry. |
| 500 | other Go error text | Genuine server-side failure. Do not parse the body; retry once, then report. |

## Retry and rate-limit posture

Counter publishes no rate limits and returns **no** `RateLimit-*`,
`X-RateLimit-*` or `Retry-After` headers, and never issues a 429. `query` is a
safe read, so a bounded retry with backoff is fine. Do not hammer it — this is a
donation-funded service run by one maintainer on a small VPS.
