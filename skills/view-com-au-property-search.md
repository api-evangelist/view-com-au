---
name: Search Australian property listings on View.com.au
description: Find on-market properties for sale, auction, lease or sold across Australia using View.com.au's public MCP server — by suburb, city, LGA, school catchment, street or lat-long radius, with price, bedroom, bathroom, car space, land size and feature filters.
api: mcp/view-com-au-mcp.yml
surface: https://mcp.view.com.au/mcp
operations: [propertySearch]
generated: '2026-07-26'
method: generated
source: mcp/view-com-au-mcp-tools.json
---

# Search Australian property listings

View.com.au exposes one search tool over its Australian listing corpus. There is no
API key, no account and no agreement — POST JSON-RPC 2.0 and it answers.

## Connect

- `POST https://mcp.view.com.au/mcp`
- `Content-Type: application/json`
- `Accept: application/json, text/event-stream` — both media types are required
- Send no `Authorization` header. There is no credential.
- Optional: call `initialize` first. It is not required; `tools/call` works cold.

## Call `propertySearch`

Six arguments are **required by the schema** even though they look optional:
`page`, `sortBy`, `saleMethod`, `includeP360Properties`, `includeSurrounding`,
`excludeUnderContract`. Omitting any of them is a validation error.

```json
{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"propertySearch","arguments":{
  "locations":[{"state":"vic","suburbName":"Fitzroy","suburbNameSlug":"fitzroy","postcode":"3065"}],
  "page":1,"sortBy":"recommended","saleMethod":["Sale"],
  "includeP360Properties":false,"includeSurrounding":false,"excludeUnderContract":false}}}
```

### Location rules — the most common failure

- `state` is required on every `locations[]` entry: `act, nsw, nt, qld, sa, tas, vic, wa, ot`.
- A human name must be paired with its slug, lowercase-hyphenated:
  - `suburbName` also needs `suburbNameSlug` **and** `postcode`
  - `streetName` also needs `streetNameSlug`
  - `lgaName` also needs `lgaSlug`
  - `schoolName` also needs `schoolNameSlug`
- `locations` is an array — search several suburbs in one call rather than looping.
- Ambiguity: a bare major-city name (Sydney, Melbourne, Brisbane, Perth, Adelaide,
  Canberra) with no postcode is treated as a **city**. If the user's phrasing could
  mean either a city or a same-named suburb, ask them — do not assume.
- Radius search: `coordinates: {latitude, longitude, radius}`, radius 0–50 km
  (default 5). `state` is still required in `locations[]`.

### Filters

`propertyTypes` (House, Apartment & Unit, Studio, Townhouse, Land, Villa, Rural),
`priceFrom`, `priceTo`, `bedrooms`, `bathrooms`, `carparks`, `landSizeMin`,
`landSizeMax`, `features`, `exactBeds`, `exactBaths`, `exactCars`,
`inspectionStartAt`, `excludeUnderContract`, `includeSurrounding`.

`saleMethod` accepts `Lease`, `Sale`, `Sold`, `Auction` — this is how you switch
between the for-sale, rental and sold corpora. `includeP360Properties: true` folds
View's off-market "Property 360" inventory into on-market results.

`sortBy`: `recommended` (default), `price-asc`, `price-desc`, `date-desc`,
`updatedAt-desc`, `suburb-asc`, `suburb-desc`, `bedrooms-desc`, `bedrooms-asc`.

## Read the response

The response is a `text/event-stream` frame. Parse the `data:` line as JSON, then
read **`result.structuredContent`**, not the prose in `result.content[].text`:

- `structuredContent.text` — a one-line natural-language summary
- `structuredContent.listings[]` — the records: `propertyId`, `address`
  (street, suburb, state, postcode, unitNumber, streetNumber, fullAddress,
  coordinates), `price.display`, `details` (bedrooms, bathrooms, carSpaces,
  propertyType), `media.heroImage`, `listingStatus`, `links.detailPage`,
  `agency`, `agents[]`

Carry `propertyId` forward to the listing-detail skill.

## Paginate

`page` starts at 1 and there is no page-size argument; a page returned 10 records in
testing. No total count, `has_more` or cursor is returned — increment `page` until
`listings` comes back empty.

## Respect the quota

100 requests per 300 seconds, anonymous and shared. The response headers carry
`ratelimit-limit`, `ratelimit-remaining` and `ratelimit-reset` — read them and back
off. There is no identity, so there is no way to ask for more.

## Handle errors

HTTP is always 200. Check both shapes:

- `error.code == -32603` — argument validation failed. `error.message` is a JSON
  array of validator issues; read `path` to find the offending argument. Usually a
  missing slug/postcode pairing or a missing required flag. Fix and retry once.
- `error.code == -32601` — method not implemented (only `initialize`, `tools/list`
  and `tools/call` exist).
- `result.isError == true` — tool-level failure, e.g. `Unknown tool: <name>`.

Full catalogue: `errors/view-com-au-error-codes.yml`.

## Use the data responsibly

Listings, agency and agent records are proprietary View content, not open data, and
include personal information (agent names, emails, phone numbers). Free to call is
not free to redistribute. Do not use returned agent contact details for outbound
contact.
