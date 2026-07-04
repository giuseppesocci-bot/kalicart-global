# KaliCart Global — Public MCP Server

Federated commerce search across independent WooCommerce merchants. Keyless, read-only, no registration required.

- **Endpoint:** `https://dashboard.kalicart.com/mcp-public` (Streamable HTTP)
- **Registry:** [`io.github.giuseppesocci-bot/kalicart-global`](https://registry.modelcontextprotocol.io/v0/servers?search=kalicart) — official MCP registry, status `active`
- **Spec & docs:** https://bridge.kalicart.com/mcp/
- **ARC protocol:** https://bridge.kalicart.com/spec/

This repository is the public interface for the server: documentation, issue tracking, and security contact. The server itself is a hosted service; its source is not published here.

## What it does

KaliCart Global exposes a federated index of real product offers from independent WooCommerce merchants that opted in via the ARC (Agent-Readable Catalog) protocol. Prices and availability are merchant-authoritative. Every product carries a direct storefront URL for checkout handoff — the server is read-only and never transacts.

## Tools

All five tools are public, keyless, read-only, and idempotent.

| Tool | Purpose |
|---|---|
| `global_search` | Search the federated index by free text (`q`) and/or canonical category (`leaf`), with facet filters (brand, gender, color, price range, stock). Returns offers with merchant-authoritative prices, UCP `availability_status`, storefront URLs and canonical category leaves. |
| `get_product` | Full product detail by `p2209_id` (obtained from `global_search`): price, availability, attributes, variants, direct storefront URL. |
| `lookup_merchant` | Check whether a merchant domain runs an ARC-compliant catalog (KaliCart Bridge). Returns bridge version, discovery URL and federated-indexing consent flags. A miss schedules a background probe. |
| `list_merchants` | List participating merchants with domain, storefront URL and product count. |
| `list_categories` | List canonical category leaves with product counts; supports `parent` filtering. |

Typical flow: `list_categories` / `list_merchants` to understand coverage → `global_search` to find offers → `get_product` on finalists → hand off to the merchant storefront URL.

### Behavior notes

- `global_search` with neither `q` nor `leaf` returns an empty result set, not an error.
- `list_categories` without `parent` returns the full leaf list.
- Free-text queries match native merchant catalog text; there is no server-side translation. Use `leaf` and facet filters for language-neutral retrieval.
- Price filters operate in each offer's merchant currency; no FX conversion is applied.
- No pagination: use `limit` (max 25 for offers, 50 for merchants).

## Feedback

If you are evaluating or integrating this server and hit unexpected behavior, [open an issue](../../issues/new/choose). Including the UTC timestamp of your requests lets us correlate with server logs.

For security matters, see [SECURITY.md](SECURITY.md) — please do not report vulnerabilities via public issues.

## Related

- [KaliCart Bridge](https://bridge.kalicart.com) — the WooCommerce plugin that makes a merchant catalog agent-readable (ARC)
- [kalicart-mcp](https://github.com/giuseppesocci-bot/kalicart-mcp) — per-site MCP plugin for WordPress

---
Maintained by [Save The Brain](https://bridge.kalicart.com) · Giuseppe Socci

