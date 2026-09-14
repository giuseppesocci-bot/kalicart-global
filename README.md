# KaliCart Global — Federated Commerce Search for AI Agents

**One query across many independent WooCommerce stores — real products, merchant-authoritative prices, indexed snapshots with a documented live-verification path back to the merchant. Keyless, read-only, no registration.**

KaliCart Global is a public [Model Context Protocol](https://modelcontextprotocol.io) server that lets an AI agent search real product offers across independent merchants that opted in through the ARC (Agent-Readable Catalog) protocol. Instead of scraping a storefront and guessing at price and stock, an agent queries a structured, federated index and gets data it can trust — then hands the shopper off to the merchant's own store to complete the purchase. The server itself never transacts.

- **Endpoint:** `https://global.kalicart.com/mcp-public` (remote, Streamable HTTP) — `https://dashboard.kalicart.com/mcp-public` also works as a compatible alias
- **Registry:** [`io.github.giuseppesocci-bot/kalicart-global`](https://registry.modelcontextprotocol.io/v0/servers?search=kalicart) — official MCP registry, status `active`
- **Docs:** https://bridge.kalicart.com/mcp/ · **ARC protocol:** https://bridge.kalicart.com/spec/ · **Provider authorization:** https://global.kalicart.com/providers/
- **Legal:** https://global.kalicart.com/terms/ (Terms of Use, including optional federated provider-delivery channels) · https://global.kalicart.com/privacy/ (Privacy Notice)

## Why it exists

Crawling a product page works when a machine only needs to *read* it. It breaks the moment an agent needs to *act* on a real price and real stock — the price may be stale, the stock may be phantom, the variant may not exist. KaliCart's approach is to make a catalog **computable** rather than merely crawlable: queryable and authoritative by design, served as an indexed snapshot with a documented handoff back to the merchant's own Bridge for live verification before checkout.

Reading a catalog this way is also far cheaper. A companion case study on the Bridge layer measured a live 626-product catalog served to an agent in **8,000 tokens instead of 196,595** — a ~24× reduction, small enough to fit a listing inside a model's context window instead of overflowing it ([read it](https://bridge.kalicart.com/blog/woocommerce-agent-token-cost/)). Global brings the same structured, read-cheap surface to many merchants at once.

## Connect

Add it to your MCP client as a remote (Streamable HTTP) server — no API key or account required:

```json
{
  "mcpServers": {
    "kalicart-global": {
      "url": "https://global.kalicart.com/mcp-public"
    }
  }
}
```

## Tools

All five tools are public, keyless, read-only, and idempotent.

| Tool | Purpose |
|---|---|
| `global_search` | Search the federated index by free text (`q`) and/or canonical category (`leaf`), with facet filters (brand, gender, color, price range, stock). Returns offers with merchant-authoritative prices, UCP `availability_status`, storefront URLs and canonical category leaves. |
| `get_product` | Full product detail by `p2209_id` (obtained from `global_search`): price, availability, attributes, variants, direct storefront URL. |
| `lookup_merchant` | Check whether a merchant domain runs an ARC-compliant catalog (KaliCart Bridge). Returns bridge version, discovery URL and federated-indexing consent flags. A miss schedules a background probe. Federated-indexing consent is separate from a merchant's optional authorization of a named external provider (see Legal above); this tool does not report per-provider authorization state. |
| `list_merchants` | List participating merchants with domain, storefront URL and product count. |
| `list_categories` | List canonical category leaves with product counts; supports `parent` filtering. |

Typical flow: `list_categories` / `list_merchants` to understand coverage → `global_search` to find offers → `get_product` on finalists → hand off to the merchant storefront URL.

### Behavior notes

- `global_search` with neither `q` nor `leaf` returns a `query_required` error (`guidance_code: USE_Q_OR_LEAF`), not an empty result set — pass at least one.
- `list_categories` without `parent` returns up to 100 leaves per call (`truncated: true` plus `total` and `complete_via` when there are more); use `parent` filtering or the paging hint to walk the full set.
- Search results and product details are indexed snapshots (`provenance.fetched_at`), not a live database read — verify the selected offer via its Bridge handoff before treating price or stock as final.
- Free-text queries match native merchant catalog text; there is no server-side translation. Use `leaf` and facet filters for language-neutral retrieval.
- Price filters operate in each offer's merchant currency; no FX conversion is applied.
- No pagination: use `limit` (max 25 for offers, 50 for merchants).

This repository is the public interface for the server — documentation, issue tracking and security contact. The server is a hosted service; its source is not published here.

## Feedback

If you are evaluating or integrating this server and hit unexpected behavior, [open an issue](../../issues/new/choose). Including the UTC timestamp of your requests lets us correlate with server logs.

For security matters, see [SECURITY.md](SECURITY.md) — please do not report vulnerabilities via public issues.

## Related

- **[KaliCart Bridge](https://bridge.kalicart.com)** — the free WooCommerce plugin that makes a single merchant's catalog agent-readable (ARC). Bridge is the *door* on each store; Global is the *federated index* across many such doors.
- **[kalicart-mcp](https://github.com/giuseppesocci-bot/kalicart-mcp)** — per-site MCP plugin for WordPress content.

---
Maintained by [Save The Brain](https://bridge.kalicart.com) · Giuseppe Socci

