---
name: Deploy a Metorial provider and open an agent session
description: >-
  Browse the Metorial catalog, deploy a provider (MCP server), wire its auth,
  and open a runtime session so an AI agent can call the provider's tools.
api: https://metorial.com/api
operations:
  - providerListings.list
  - providers.get
  - providerDeployments.create
  - providerDeployments.authConfigs.create
  - providerDeployments.setupSessions.create
  - sessions.create
  - sessions.providers.create
  - providers.tools.list
  - providerRuns.list
---

# Deploy a Metorial provider and open an agent session

Use the Metorial REST API (`https://api.metorial.com`) to connect an AI agent to a
tool. Authenticate every request with `Authorization: Bearer metorial_sk_...` (a
secret key scoped to your development or production instance).

## Steps

1. **Find a provider.** `providerListings.list` to browse the catalog; `providers.get`
   to inspect a specific provider (its versions, specifications, and auth methods).
2. **Deploy it.** `providerDeployments.create` to create a deployment of the provider
   in your instance.
3. **Configure auth.** `providerDeployments.authConfigs.create` to attach auth config.
   For OAuth providers, `providerDeployments.setupSessions.create` starts a Provider
   Setup Session so an end user can authorize (handle `OAuthRequiredError` if the grant
   later expires).
4. **Open a session.** `sessions.create`, then `sessions.providers.create` to attach the
   deployed provider to the session. The session is the runtime layer your agent connects to.
5. **Discover and call tools.** `providers.tools.list` to enumerate the tools the session
   exposes; the agent calls them over the session's MCP URL.
6. **Observe.** `providerRuns.list` (and `providerRuns.getLogs`) to inspect tool-call runs.

## Rules

- Cursor pagination: pass `after` / `before`; read `pagination.has_more_after`.
- Rate limits: 5000 req / key / 10 min (production), 100 (development); back off on `429`.
- Errors are a JSON envelope (`message`, `status_code`, `type`); SDK classes:
  `AuthenticationError`, `NotFoundError`, `OAuthRequiredError`, `RateLimitError`.
- Pin the API version (date-based, current `2026-01-01`) for stability.
