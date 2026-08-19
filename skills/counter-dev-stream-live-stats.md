---
name: counter-dev-stream-live-stats
description: Open Counter's Server-Sent Events feed to read live and archived visit aggregates as they update. Use when asked what is happening on a site right now, for a live visitor count, or for the account's full current snapshot including its oldest archived date.
api: counter-dev:counter-dev-stats-api
operations:
  - dump
generated: '2026-08-13'
method: generated
source: openapi/counter-dev-stats-api-openapi.yml, asyncapi/counter-dev-stats-asyncapi.yml, conventions/counter-dev-conventions.yml
---

# Stream live Counter stats

The `dump` operation is Counter's only event surface. It is a **Server-Sent
Events** stream — one-way HTTP, not a WebSocket — and it is the feed the Counter
dashboard itself consumes. Counter has no webhooks, so if you need
change-driven behaviour, this is the mechanism.

## When to use this instead of `query`

Use `dump` for **now**: the current day's aggregates, a live count, or the
account's full snapshot in one call. Use the `counter-dev-read-site-stats` skill
(`query`) for **history over a date range** — `dump` gives you only two fixed
archive windows and cannot be asked for an arbitrary range.

## Steps

1. **Open the stream.** `GET /dump` on `https://counter.dev`, with
   `Accept: text/event-stream`.

   ```
   GET /dump?user=ACCOUNT&token=TOKEN&utcoffset=0
   ```

   No credential needed against the public demo account:

   ```
   GET /dump?demo=1
   ```

   `utcoffset` is the caller's UTC offset and controls how visits are bucketed
   into local days and hours. It is clamped server-side to the range −12..+14.

2. **Read typed frames.** Every frame is `data: <json>` — Counter does **not**
   use the SSE `event:` field, so you must dispatch on the `type` key inside the
   JSON payload, not on the event name. It also emits no `id:` field, so
   `Last-Event-ID` resumption is unavailable; a dropped connection means
   reopening from scratch.

   The frames arrive in this order:

   | `type` | When | Payload |
   |---|---|---|
   | `oldest-archive-date` | First, once | `"YYYY-MM-DD"` — the earliest date with archived data. Use it to bound any date picker or historical query you build. |
   | `archive` | Second, once | Two precomputed windows keyed `"-7:-2"` and `"-30:-2"` (day offsets relative to today). |
   | `dump` | Third, then repeatedly | The full current snapshot. Re-sent whenever new visits arrive, throttled server-side to at most one per second. |
   | `nouser` | Instead of everything above | Authentication failed. The server then closes. |

3. **Handle `nouser` explicitly — this is the trap.** Counter sends `nouser`
   inside an **HTTP 200** response. A client that checks only the status code
   will believe it is authenticated and then sit on a stream that has already
   closed. Always inspect the first frame's `type` before treating the stream as
   live.

4. **Read the `dump` payload.**

   ```json
   {
     "type": "dump",
     "payload": {
       "sites": {
         "example.com": {
           "count": 812,
           "logs": {"...": 1},
           "visits": {
             "day": {}, "yesterday": {}, "month": {}, "year": {}, "all": {}
           }
         }
       },
       "user": {
         "id": "account",
         "token": "…",
         "uuid": "…",
         "isSubscribed": false,
         "prefs": {"utcoffset": "0"}
       },
       "meta": {}
     }
   }
   ```

   - `sites[host].visits` uses the same dimension → value → count shape as
     `query`, split across five windows.
   - `meta` is empty for a normal session, `{"demo":"1"}` on the demo account, and
     `{"sessionless":"1"}` when you authenticated with the `user`+`token` pair —
     a useful confirmation of which mode you are actually in.

5. **Handle the secret in the payload.** Every `dump` frame contains
   `payload.user.token` — the account's read-only share token. **Never** surface
   it in output, a transcript, or a log. Strip it as soon as you parse the frame.

6. **Close the connection when you are done.** This is a long-lived stream with
   no server-side write timeout, and each open connection holds a Redis
   connection on Counter's side. Take your snapshot, then disconnect. Do not
   leave it open to poll.

## Errors

| Status / frame | Meaning |
|---|---|
| 200 + `nouser` frame | No session, no valid token pair, no `demo`. Not an HTTP error — detect it from the frame. |
| 403 `Forbidden` | Returned when the request is rejected before the stream opens. |
| 500 + raw Go error text | Server-side failure. Body is not a stable contract; do not parse it. |

No `Retry-After` and no rate-limit headers are returned. Reconnect with backoff,
and remember the server already throttles dumps to one per second — reconnecting
faster gains you nothing.
