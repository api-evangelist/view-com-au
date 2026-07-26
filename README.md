# View.com.au (view-com-au)

View.com.au is an Australian residential property portal operated by View Media Group Pty Ltd (ACN 619 657 680) through view.com.au Pty Ltd (ACN 088 369 395), based in Victoria. It is the challenger portal to the REA Group / Domain duopoly, carrying for-sale, for-rent and sold listings, agency and agent profiles, Victorian sales and auction results, off-market "Property 360" property records with price estimates and planning/zoning overlays, plus home loans and editorial. Its API posture is unusual and worth stating precisely: the company publishes no developer portal, no OpenAPI, and no self-serve API program, and every HTML page on view.com.au is behind DataDome bot protection that returns HTTP 403 to non-browser clients — yet it operates a completely public, unauthenticated Model Context Protocol server at https://mcp.view.com.au/mcp that returns live listing data to any anonymous agent. The human web is closed to machines while the agent surface is wide open. RESO plays no part here: RESO is a US National Association of REALTORS construct, and the RESO certified-organizations directory lists no Australian organizations at all.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/view-com-au/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/view-com-au/refs/heads/main/apis.yml)

## Tags

- Real Estate
- Australia
- Property Listings
- Property Portal
- PropTech
- Rentals
- Off-Market Property Data
- Model Context Protocol
- Agent-Native

## Timestamps

- **Created:** 2026-07-26
- **Modified:** 2026-07-26

## APIs

### View.com.au Property MCP Server

A publicly reachable, unauthenticated Model Context Protocol server (streamable HTTP, serverInfo name `view-com-au-mcp-server` version 1.0.0, protocol 2024-11-05) exposing three tools over View.com.au's Australian property data — `propertySearch` (on-market listings by state/city/suburb/postcode/street/LGA/school or lat-long radius, filtered by property type, price, beds, baths, car spaces, land size, features and sale method), `propertyDetail` (a single on-market listing by listingId, including inspection times, floor plans, Statement of Information, nearby schools, zoning, agency and agent), and `offMarketPropertyDetail` (an off-market property by SEO slug, including price estimates, zoning code and planning overlays, property history and images). Verified live on 2026-07-26 by anonymous JSON-RPC initialize, tools/list and a tools/call to propertySearch that returned real Fitzroy VIC listings — no API key, no signup, no agreement. Rate limited at 100 requests per 300 seconds. No human-readable documentation page for this server was found.

- **Human URL:** [https://view.com.au/](https://view.com.au/)
- **Base URL:** `https://mcp.view.com.au/mcp`

#### Tags

- Model Context Protocol
- Property Listings
- Property Search
- Off-Market Property Data
- Australia

#### Properties

- [MCP Server](mcp/view-com-au-mcp-tools.json)
- [MCP Server](mcp/view-com-au-mcp-initialize.json)
- [Website](https://view.com.au/)

## Common Properties

- [Website](https://view.com.au/)
- [Website](https://www.viewmediagroup.com.au/)
- [Blog](https://view.com.au/news/)
- [GitHub Organization](https://github.com/realestateview)

## RESO Posture

**RESO certified: no.** No RESO reference was found anywhere on a reachable View.com.au host. The RESO certified-organizations directory at https://www.reso.org/certificates/ was fetched in full (HTTP 200, 416,233 bytes) and contains zero occurrences of "Australia", "View", "View Media", "REA Group" or "Domain Group" while listing known certified bodies such as Bright MLS and CRMLS. There is no RESO Web API certification, no Data Dictionary certification, no Universal Property Identifier support, and no OData `$metadata` document. RESO is a US National Association of REALTORS construct with no counterpart in the Australian market, where listing intake runs on commercial portal/CRM agreements and the de facto REAXML format owned by the dominant competitor.

## Access Gate

**self-serve.** The MCP server accepted an anonymous JSON-RPC POST with no API key, no OAuth token, no account, no application form, no licence agreement, no MLS or board membership, and no broker sponsorship, and returned real on-market listings. There is nothing to sign and nothing to join.

The contrast surfaces are gated and undocumented: `api.view.com.au` is a real AWS API Gateway that returns `MissingAuthenticationTokenException`, `sandbox.api.view.com.au` returns 403 with no public onboarding path, and `/api/pubui/*` is the site's internal front-end API, explicitly disallowed in robots.txt.

## Open Data

**No.** Free to call is not the same as open data. Every record behind the MCP server is proprietary portal content sourced from agency feeds.

## Maintainers

- Kin Lane (kin@apievangelist.com)
