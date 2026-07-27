---
name: Retrieve a full View.com.au listing
description: Given a View.com.au listing id, retrieve the complete on-market property record — description, price, inspection times, floor plans, Statement of Information, images, zoning and planning overlays, and the listing agency and agents.
api: mcp/view-com-au-mcp.yml
surface: https://mcp.view.com.au/mcp
operations: [propertySearch, propertyDetail]
generated: '2026-07-26'
method: generated
source: mcp/view-com-au-mcp-tools.json
---

# Retrieve a full listing

`propertyDetail` is the only way to get the full record for an on-market property.
It takes a numeric listing id and nothing else.

## Get an id first

There is no id lookup by address. Ids come from `propertySearch` — the `propertyId`
field on each result (see `skills/view-com-au-property-search.md`). They also appear
at the end of a public detail URL, e.g.
`https://view.com.au/property/vic/fitzroy-3065/303-41-kerr-street-18071535/` → `18071535`.

## Call `propertyDetail`

```json
{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"propertyDetail","arguments":{"listingId":18071535}}}
```

`listingId` must be an **integer**, not a string — passing `"18071535"` returns a
`-32603` validation error. Same transport as every other call: POST to
`https://mcp.view.com.au/mcp` with `Accept: application/json, text/event-stream` and
no credentials.

## What comes back

`result.structuredContent` is a flat listing object:

- Identity and address: `id`, `address`, `propertyType`, `saleMethod`, `status`
- Numbers: `bedrooms`, `bathrooms`, `carparks`, `landSize`, `landSizeSystem`, `price`
- Copy: `heading`, `description`, `features[]`
- Media: `heroImageUrl`, `images[]` (`url`, `sequence`), `floorPlans[]` (`url`)
- `statementOfInfo[]` (`url`) — the Victorian statutory price-disclosure document
- `inspections[]` — `id`, `startAt`, `endAt`, `calendarUrl`
- `nearbySchoolsRev` — nearby schools, often null
- `agencyId`, `agentIds[]`, plus fully embedded `agency` and `agents[]` objects
- Zoning is documented on this tool (zoning code, description, local government
  area, and planning overlays such as bushfire, flood and heritage); it was not
  present on every record tested, so treat it as optional and check before use.

Field-by-field map: `data-model/view-com-au-data-model.yml`.

## Practical notes

- **Agency and agent have stable numeric ids but no lookup tool.** The only way to
  read an agency or agent is as an embedded object on a listing. If you need an
  agency profile, collect it from listings you already fetched.
- **`isLlmChatEnabled`** appears on both agency and agent records — View models
  per-office and per-agent AI chat availability as data.
- **A dead id is indistinguishable from an outage.** A listing id that no longer
  resolves returns `result.isError: true` with
  `Error getting listing detail: Failed to fetch listing detail from API`. Treat it
  as not-found, re-run `propertySearch`, and do not retry in a loop — retries burn
  the shared quota.
- **Nothing is cacheable server-side.** No ETag, no conditional requests,
  `cache-control: no-cache`. Cache client-side against the
  100-request/300-second ceiling.

## Handle errors

HTTP is always 200. Check `result.isError` as well as `error` — a failed lookup
arrives as a normal successful JSON-RPC result. See
`errors/view-com-au-error-codes.yml`.

## Use the data responsibly

This record carries the richest personal data on the surface: agent `email`,
`phone` and `mobile`, plus inspection schedules. Honour `isMobileHidden` and
`isWebHidden` on agent objects, do not redistribute contact details, and do not use
them for outbound contact.
