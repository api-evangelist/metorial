---
name: Create a Magic MCP endpoint and issue a scoped token
description: >-
  Assemble multiple providers into a Magic MCP endpoint/group and mint a scoped
  MCP token so an external client (Claude, ChatGPT, Cursor, Copilot) can connect
  via a standard MCP URL.
api: https://metorial.com/api
operations:
  - magicMcpServers.list
  - magicMcpServers.tools
  - magicMcpEndpoints.create
  - magicMcpEndpoints.addServers
  - magicMcpGroups.create
  - magicMcpGroups.addServers
  - magicMcpTokens.create
  - magicMcpTokens.addGroups
  - magicMcpSessions.list
---

# Create a Magic MCP endpoint and issue a scoped token

Magic MCP turns a set of Metorial providers into a single standard MCP URL that any
MCP-compatible client connects to — no per-client OAuth. Authenticate with
`Authorization: Bearer metorial_sk_...`.

## Steps

1. **Inspect available MCP servers.** `magicMcpServers.list`; `magicMcpServers.tools` to
   see the tools a server exposes.
2. **Create an endpoint.** `magicMcpEndpoints.create`, then `magicMcpEndpoints.addServers`
   to attach the MCP servers your users should reach.
3. **Group for access control.** `magicMcpGroups.create` and `magicMcpGroups.addServers`
   to bundle servers into a governed group.
4. **Mint a token.** `magicMcpTokens.create`, then `magicMcpTokens.addGroups` to scope the
   token to specific groups. The token backs the standard MCP URL you hand to the client.
5. **Monitor usage.** `magicMcpSessions.list` to review connections.

## Rules

- Cursor pagination (`after`/`before`, `has_more_after`).
- Back off on `429`; production limit 5000 req / key / 10 min.
- Per-user isolation and access control are enforced by the token/group scoping — never
  share a secret key with a client; the MCP URL + token is the client-facing surface.
