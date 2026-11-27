# BDA MCP — Jyotish, Panchang & Sanatan Dharma tools for AI agents

Remote [Model Context Protocol](https://modelcontextprotocol.io) server by [Bharat Dharma Academy](https://www.bda.ai), an Australian registered charity preserving Sanatan Dharma knowledge (ABN 54 683 667 656).

**Endpoint:** `https://jyotish-api.bda.ai/mcp` (JSON-RPC 2.0 over HTTPS — no install, no API key for the evaluation tier)

The server exposes **22 public, read-only tools** backed by the same deterministic engines that power [bda.ai](https://www.bda.ai): Swiss-Ephemeris Panchang computation, festival calendars, muhurat windows, Jyotish concept explanations, and a source-grounded Sanatan knowledge corpus with citations and provenance metadata.

## Quick start

Any MCP client that supports remote HTTP servers:

```json
{
  "mcpServers": {
    "bda-jyotish": {
      "type": "http",
      "url": "https://jyotish-api.bda.ai/mcp"
    }
  }
}
```

Claude Code:

```bash
claude mcp add --transport http bda-jyotish https://jyotish-api.bda.ai/mcp
```

Raw JSON-RPC:

```bash
curl -X POST https://jyotish-api.bda.ai/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Tools

| Tool | What it returns |
|---|---|
| `get_today_panchang` | Daily Panchang (tithi, nakshatra, yoga, karana, sunrise/sunset) for a city/date |
| `get_panchang_by_date_city` | Panchang JSON for any date + location |
| `get_festival_calendar` | Hindu festival feed / calendar export |
| `get_udaya_lagna` | Ascendant-sign windows from local sunrise |
| `get_tithi_info` | Tithi context via public Panchang evidence endpoints |
| `get_tithi_calendar` | Annual start/end spans for one tithi in a location |
| `get_nakshatra_info` | Nakshatra context by date/location |
| `get_kundali_explanation` | Public Jyotish concept explanations |
| `search_sanatan_encyclopedia` | Canonical public Sanatan Encyclopedia pages |
| `get_mantra_meaning` | Reviewed-source discovery guidance for mantra meanings |
| `get_jyotish_glossary_term` | Jyotish glossary / concept metadata |
| `get_public_bda_feature_summary` | BDA service and API discovery metadata |
| `list_public_sitemap_urls` | Paged public URL catalog from BDA sitemaps |
| `get_public_page_metadata` | Safe metadata for a single allowlisted public URL |
| `list_public_api_endpoints` | Public evaluation endpoints from the filtered OpenAPI contract |
| `search_temples` | Search the public BDA temple directory (name, city, state, deity, nearby) |
| `get_temple` | One public temple record by UUID or `/teerth` route slug |
| `list_blog_posts` | Published BDA blog posts (paged) or text search |
| `list_mantras` | Classical mantras (navagraha beej, deity primary) in Devanagari/IAST |
| `get_mantra` | One mantra by slug with source metadata |
| `list_scriptures` | Public Sanatan scripture PDFs on bda.ai |
| `get_scripture` | One public scripture PDF by slug with detail URL and rights label |

Live machine-readable catalog: [`/mcp/tools.json`](https://jyotish-api.bda.ai/mcp/tools.json)

## Discovery & documentation

- MCP documentation page: <https://jyotish-api.bda.ai/docs/mcp>
- Full public REST API docs (OpenAPI): <https://jyotish-api.bda.ai/api-docs>
- A2A agent card: <https://jyotish-api.bda.ai/.well-known/agent-card.json>
- `llms.txt`: <https://jyotish-api.bda.ai/llms.txt> and `llms-full.txt`: <https://jyotish-api.bda.ai/llms-full.txt>
- AI sitemap: <https://jyotish-api.bda.ai/ai-sitemap.json>
- A2A docs: <https://jyotish-api.bda.ai/docs/a2a>

## Daily Panchang: use MCP, not REST

Daily Panchang JSON is **MCP-only** (`tools/call get_today_panchang`). The direct REST endpoint `/panchang/daily` (also `/panchang/details`, `/panchang/month`, `/panchang/rashifal`) is reserved for the BDA product: external callers always get `503` with `detail.code: capacity_reserved`, which is a standing policy reservation, so do not retry it. Other Panchang endpoints (hora, choghadiya, festivals, tithis, ...) are open under evaluation access.

## Rate limits (evaluation access)

Per client network address and endpoint tier; ceilings, not reserved capacity, and not a contractual free tier (no SLA, commercial or reseller rights).

| Tier | Rate | Burst | Deadline |
|---|---|---|---|
| `public_read` | 60/minute | 12/second | 30 s |
| `public_compute` | 20/minute | 4/second | 60 s |
| `public_extended` | 6/minute | 2/second | 180 s |
| `/mcp` | 120/minute | n/a | per-tool deadlines apply |

`429` returns `rate_limit_exceeded` with `Retry-After`; `504` returns `request_timeout`. `503 capacity_saturated` is transient (retry with backoff); `503 capacity_reserved` is not. Source: <https://jyotish-api.bda.ai/api-docs>.

## Notes

- **Read-only and public.** Authenticated user tools (personal Kundali, dashboards, saved reports) are intentionally not exposed here.
- **Free evaluation tier** with the rate limits above (`Retry-After` on 429). Commercial access: [info@bda.ai](mailto:info@bda.ai).
- Responses carry **citations, provenance and trust-tier metadata** — keep them intact and visible to downstream users.
- Jyotish content is spiritual/cultural guidance, not medical, legal or financial advice.

## License

The documentation in this repository is MIT-licensed. The hosted API itself is governed by the usage terms at [jyotish-api.bda.ai/api-docs](https://jyotish-api.bda.ai/api-docs#usage-rules).
