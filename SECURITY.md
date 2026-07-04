# Security Policy

KaliCart Global is a hosted, keyless, read-only MCP server. It performs no writes, holds no user accounts, and never handles payments — but we take the integrity of the surface it exposes to AI agents seriously.

## Reporting a vulnerability

**Please do not report security issues through public GitHub issues.**

Report privately via GitHub's private vulnerability reporting on this repository ("Report a vulnerability" under the Security tab), or by email to **bug@kalicart.com**.

Please include:

- The tool or endpoint involved
- UTC timestamp of your requests (lets us correlate with server logs)
- Steps to reproduce, and expected vs. observed behavior

You will receive an acknowledgment within 72 hours. We ask for reasonable time to remediate before public disclosure.

## Scope

In scope: the public MCP endpoint (`dashboard.kalicart.com/mcp-public`), its five tools, and the discovery surface at `bridge.kalicart.com`.

Out of scope: individual merchant storefronts (report to the merchant), and volumetric denial-of-service testing.

