# Counter (counter-dev)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
