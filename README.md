# View.com.au (view-com-au)

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
