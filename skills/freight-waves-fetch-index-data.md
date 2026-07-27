---
name: Fetch SONAR freight-market index data
description: >-
  Authenticate to the FreightWaves SONAR API, resolve a valid index and
  qualifier, then retrieve index values for a date or date range.
api: openapi/freight-waves-sonar-openapi.yml
operations: [authenticate, lookupIndexes, lookupQualifiers, getIndexData, getIndexDataRange]
generated: '2026-07-19'
method: generated
---

# Fetch SONAR freight-market index data

Retrieve a FreightWaves SONAR index value (e.g. OTRI — Outbound Tender Reject
Index) for a market, lane, or level over a date or date range. Base URL:
`https://api.freightwaves.com`.

## Auth
- Call `authenticate` — `POST /Credential/authenticate` with a JSON body
  `{"username": "...", "password": "..."}`. It returns a bearer token valid for
  one year. Send it on every subsequent request as
  `Authorization: Bearer <token>`. Refresh by re-authenticating; this does not
  invalidate prior tokens.

## Steps
1. **Resolve the index.** Call `lookupIndexes` (`GET /Lookup/Indexes`) to confirm
   the ticker you want is valid (e.g. `OTRI`). Lookup calls are free.
2. **Resolve the qualifier.** Call `lookupQualifiers` (`GET /Lookup/Qualifiers`)
   to pick a valid market/lane/level code (e.g. `ATL`, `NATIONAL`, `XMKT`).
3. **Fetch a single date.** Call `getIndexData`
   (`GET /data/{index}/{qualifier}/{date}`), e.g.
   `/data/OTRI/ATL/2019-02-20`. Dates are `YYYY-MM-DD`.
4. **Or fetch a range.** Call `getIndexDataRange`
   (`GET /data/{index}/{qualifier}/{startDate}/{endDate}`) for a series.

## Rules
- **Billing:** `/data/...` calls are billable; all `/Lookup/...` calls are free.
  Resolve indexes/qualifiers with free lookups before spending on data calls.
- **Rate limit:** 100 requests/minute globally. On `429`, back off — continued
  over-limit requests trigger a ~10 minute blacklist.
- **Errors:** `401` means a missing/invalid/expired token — re-authenticate.
  See `errors/freight-waves-problem-types.yml`.
- **No pagination:** data is addressed directly by index/qualifier/date; use the
  date range form for a series rather than paging.
