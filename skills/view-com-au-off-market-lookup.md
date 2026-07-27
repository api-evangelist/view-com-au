---
name: Look up an off-market property on View.com.au
description: Retrieve a View.com.au off-market ("Property 360") property record by SEO slug — price estimates, zoning code and planning overlays, property history, nearby schools and images — and handle the constraint that off-market slugs cannot be enumerated.
api: mcp/view-com-au-mcp.yml
surface: https://mcp.view.com.au/mcp
operations: [propertySearch, offMarketPropertyDetail]
generated: '2026-07-26'
method: generated
source: mcp/view-com-au-mcp-tools.json
---

# Look up an off-market property

`offMarketPropertyDetail` reads View's off-market inventory — properties that are
**not** listed for sale. This is the most distinctive and the most sensitive tool on
the surface.

## The identity problem, first

Off-market properties are keyed by **SEO slug**, a different identity space from the
numeric `propertyId` used for on-market listings. There is no slug search, no slug
in the on-market payload, and no off-market sitemap. In practice that means:

- You cannot enumerate off-market properties.
- You must already have a slug, from a `view.com.au` property URL a user gives you.
- Reusing an on-market listing slug does **not** work — it returns a tool-level
  error, verified 2026-07-26.

The one supported path from search: set `includeP360Properties: true` on
`propertySearch`, which folds Property 360 records into on-market results.

## Call `offMarketPropertyDetail`

All three arguments are required by the schema:

```json
{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"offMarketPropertyDetail","arguments":{
  "seo_slug":"<slug-from-a-view-property-url>",
  "isbot":true,
  "includeArchistarAndNearBySchool":true}}}
```

- `seo_slug` — string, minimum 3 characters
- `isbot` — boolean, schema default `true`. The server asks the caller to declare
  itself a bot, and the default is honest. Leave it `true`.
- `includeArchistarAndNearBySchool` — boolean, default `true`; adds Archistar
  planning data and nearby schools.

Transport is the same as every other call: POST to `https://mcp.view.com.au/mcp`
with `Accept: application/json, text/event-stream` and no credentials.

## What comes back

Documented by the tool contract: address, property type, bedrooms, bathrooms, car
spaces, land area, **price estimates**, zoning (code, description, local government
area, and planning overlays for environment, infrastructure, industry, bushfire,
flood and heritage), nearby schools, **property history** and images.

## Handle errors

HTTP is always 200. An unresolvable slug returns `result.isError: true` with
`Error getting property detail: Failed to fetch property detail from API` — the
same message you would get from an upstream failure, so not-found and outage are
indistinguishable. Do not retry a slug that failed; it is almost certainly wrong.
See `errors/view-com-au-error-codes.yml`.

## Use the data responsibly — read this before running at volume

- **These owners did not list.** An off-market record describes a property whose
  owner has not put it on the market. Treat every field as sensitive.
- **Price estimates are estimates.** They are View's automated opinion. Never
  present one to a consumer as a valuation, appraisal or professional advice.
- **Planning overlays are indicative.** Bushfire, flood and heritage overlays are
  material to a purchase decision; direct users to the responsible state or council
  authority before they rely on them.
- **Do not build prospecting lists.** This tool is the one most easily repurposed
  for unsolicited vendor outreach. `agentic-access/view-com-au-agentic-access.yml`
  marks it `audit: required` for that reason.
- **Free to call is not open data.** No licence grants redistribution, and the MCP
  surface has no terms of its own.

## Respect the quota

100 requests per 300 seconds, anonymous and shared. Read `ratelimit-remaining` and
`ratelimit-reset` from the response headers and back off.
