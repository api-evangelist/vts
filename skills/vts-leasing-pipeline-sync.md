---
name: Sync the VTS leasing pipeline
description: Pull a landlord's active leasing pipeline out of VTS — assets, spaces, deals, deal terms, financials and tenants — into an external system.
api: openapi/vts-openapi.yml
operations:
  - get_api-v1-assets
  - get_api-v1-spaces
  - get_api-v1-deals
  - get_api-v1-deals-id
  - get_api-v1-deal-terms
  - get_api-v1-deal-terms-deal-term-id-financials
  - get_api-v1-tenants
generated: '2026-07-21'
method: generated
source: openapi/vts-openapi.yml
---

# Sync the VTS leasing pipeline

Extract a landlord's active leasing activity from the VTS Lease API for reporting or downstream sync.

## Auth
- HTTP Basic: send the **API Key as username** and **API Secret as password**.
- Base URL `https://api.vts.com/api/connect/v1` (production) or `https://sandbox.vts.com/api/connect/v1` (sandbox).
- Verify credentials first: `GET /api/connect/v1` returns `<Authorized>true</Authorized>` when valid.

## Steps
1. **List assets** — `get_api-v1-assets` (`GET /api/v1/assets`). Assets are the top-level building/property. Page with `page[after]`/`page[before]` cursors (`page[size]` to tune batch size).
2. **List spaces** — `get_api-v1-spaces` (`GET /api/v1/spaces`) for the leasable sub-components of each asset.
3. **List deals** — `get_api-v1-deals` (`GET /api/v1/deals`); read a specific one with `get_api-v1-deals-id`. Each deal carries stage, requirements and deal type.
4. **List deal terms** — `get_api-v1-deal-terms` (`GET /api/v1/deal_terms`) for rentable size, term duration and tenant risks.
5. **Pull financials per term** — `get_api-v1-deal-terms-deal-term-id-financials` (`GET /api/v1/deal_terms/{deal_term_id}/financials`) for base rents, escalations, free rent, reimbursables and commissions.
6. **Resolve tenants** — `get_api-v1-tenants` (`GET /api/v1/tenants`), linking to Global Tenants for brand-name cleansing.

## Conventions
- **Pagination:** opaque base64 cursors — follow `page[after]` until exhausted; never assume offset math.
- **Filtering:** JSON:API-style `filter[...]` query params.
- **Errors:** non-2xx responses return an `errors` array (see `errors/vts-problem-types.yml`); `403` means the account/endpoint permission was not granted for that API key.
- The API is **read-only** for this flow; no idempotency key is required (none is documented).
