---
name: Publish VTS building availability to a website
description: Use the VTS Leasing Availability API to render real-time building and listing availability on an external website or listing service.
api: openapi/vts-openapi.yml
operations:
  - get_api-v1-buildings
  - get_api-v1-buildings-id
  - get_api-v1-listings-id
generated: '2026-07-21'
method: generated
source: openapi/vts-openapi.yml
---

# Publish VTS building availability to a website

The Leasing Availability API surfaces VTS Market building and listing data so you can build portfolio, building, and listing pages on your own site.

## Auth
- HTTP Basic — API Key as username, API Secret as password. The key is scoped to a single account.
- Base URL `https://api.vts.com/api/connect/v1`.

## Steps
1. **Portfolio page** — `get_api-v1-buildings` (`GET /api/v1/buildings`) returns every building on VTS Market for the account behind the API key, with its available listings. Use it to build a portfolio index. Cursor-paginate with `page[after]`.
2. **Building page** — `get_api-v1-buildings-id` (`GET /api/v1/buildings/{id}`) returns one building plus its available listings. Filter by `building_id` where supported.
3. **Listing page** — `get_api-v1-listings-id` (`GET /api/v1/listings/{id}`) returns a single listing's detail for a dedicated listing page.

## Conventions
- **Cursor pagination:** `page[after]` / `page[before]` opaque base64 cursors, `page[size]` for batch size.
- **Caching:** availability changes as deals progress — refresh on a schedule rather than per page view.
- **Errors:** an `errors` array on non-2xx (see `errors/vts-problem-types.yml`); `401` = bad credentials, `403` = the Leasing Availability endpoints were not enabled for the key.
- Read-only; no idempotency key required.
