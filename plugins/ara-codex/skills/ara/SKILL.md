---
name: ara
description: Integrate software with Ara's public REST API using a scoped API key. Use for API clients, CI, service integrations, or cloud coding-session automation.
---

# Ara API

Ara external integrations use the public REST API.

- API base URL: `https://api.ara.so/v3`
- API quickstart: `https://docs.ara.so/api-quickstart`
- Endpoint reference: `https://docs.ara.so/api-reference`

## Setup

1. Create a least-privilege `ara_` API key in Ara Settings and store it only in
   the calling service or CI secret manager. Never put it in source control,
   prompts, logs, generated files, or a repository environment file.
2. Send it as `Authorization: Bearer <api-key>`. Start with `GET /v3/self` to
   resolve the key's organization before reading or changing organization data.
3. Use only the scopes required by the integration. Treat secret values as
   write-only and do not expose them in output, errors, or telemetry.
4. Use the endpoint reference for schemas, pagination, idempotency, and error
   handling. Do not infer an endpoint from an old tool name or configuration.

## Session automation

- Create and monitor work through the documented `/v3/organizations/:orgId/
  sessions` endpoints. Return the session identifier or URL to the caller and
  use the documented status semantics.
- Scope every request to the intended organization and repository. Do not infer
  authority from a repository name or untrusted task payload.
- Surface failed mutations clearly. Retry only when the endpoint contract and
  the operation's idempotency guarantee make that safe.

## Guardrails

- Never request, print, or copy a user login token into a service integration.
- Never put an API key in an Ara session, repository secret, prompt, or artifact.
- Do not work around a missing public API scope or endpoint with an unapproved
  transport or client configuration.
