# Counter (counter-dev)

Counter (counter.dev) is an open-source, privacy-friendly web analytics service. A lightweight tracking snippet POSTs a single aggregated hit per visit to a public collect endpoint (t.counter.dev), and a token-authenticated dashboard data feed returns aggregated stats. Counter uses no cookies, no logging, and no IP fingerprinting. It is AGPL-3.0 licensed and can be self-hosted; the hosted service is pay-what-you-want.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/counter-dev/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/counter-dev/refs/heads/main/apis.yml)

## Tags

- Web Analytics
- Privacy
- Open Source
- Tracking
- Self-Hosted

## Timestamps

- **Created:** 2026-06-21
- **Modified:** 2026-06-21

## APIs

### Counter Tracking / Collect API

Public, unauthenticated collect endpoints called by the 1.1 KB tracking snippet. GET /track records one aggregated visit (referrer, screen, country, language, device) keyed to a site data-id; POST /trackpage records a pageview via the browser Beacon API. No cookies or IP addresses are stored.

- **Human URL:** [https://counter.dev/help/integration.html](https://counter.dev/help/integration.html)
- **Base URL:** `https://t.counter.dev`

#### Tags

- Tracking
- Collect
- Analytics

#### Properties

- [Documentation](https://counter.dev/help/integration.html)
- [API Reference](https://github.com/ihucos/counter.dev/blob/master/docs/script.js)
- [OpenAPI](openapi/counter-dev-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/counter-dev.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/counter-dev.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Counter Stats / Dashboard Data API

Token- or session-authenticated read endpoints that return aggregated site stats. GET /query returns JSON archive data for a date range; GET /dump is a Server-Sent Events stream pushing live and archived dump payloads. Sessionless access uses user + token query parameters; a shareable read token is resettable via /resettoken.

- **Human URL:** [https://counter.dev/dashboard.html](https://counter.dev/dashboard.html)
- **Base URL:** `https://counter.dev`

#### Tags

- Stats
- Dashboard
- Reporting

#### Properties

- [API Reference](https://github.com/ihucos/counter.dev/blob/master/backend/endpoints/query.go)
- [OpenAPI](openapi/counter-dev-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/counter-dev.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/counter-dev.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Counter Account API

Form-encoded account endpoints backing the dashboard - register, login, logout, edit account, reset token, and delete user/site. Authentication is by username and password establishing a session cookie; these are application endpoints rather than a formally published developer API.

- **Human URL:** [https://counter.dev/](https://counter.dev/)
- **Base URL:** `https://counter.dev`

#### Tags

- Account
- Authentication
- Users

#### Properties

- [API Reference](https://github.com/ihucos/counter.dev/tree/master/backend/endpoints)
- [OpenAPI](openapi/counter-dev-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/counter-dev.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/counter-dev.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [GitHub Organization](https://github.com/ihucos/counter.dev)
- [Website](https://counter.dev/)
- [Documentation](https://counter.dev/help/integration.html)
- [Plans](plans/counter-dev-plans-pricing.yml)
- [Rate Limits](rate-limits/counter-dev-rate-limits.yml)
- [Fin Ops](finops/counter-dev-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
