# Ara API integration guidance

Official, secretless API-integration guidance for Codex and Claude Code. These
plugins help an agent build against Ara's public REST API; they do not configure
a remote tool server, browser authorization, API key, header, local executable,
or hook.

## Install

### Codex

```sh
codex plugin marketplace add Aradotso/ara-mcp
codex plugin add ara@ara
```

### Claude Code

```sh
claude plugin marketplace add Aradotso/ara-mcp
claude plugin install ara@ara
```

The plugin only provides API-integration guidance. It does not sign anyone in,
request a browser approval, or create a credential.

## Build an integration

1. Create a minimally scoped, organization-pinned `ara_` key in **Settings →
   Ara API** and place it only in the calling service or CI secret manager.
2. Call the public REST API at `https://api.ara.so/v3`; start with
   `GET /v3/self` to resolve the key's organization.
3. Use the [API quickstart](https://docs.ara.so/api-quickstart) and
   [endpoint reference](https://docs.ara.so/api-reference) for schemas,
   pagination, idempotency, and error handling.
4. Rotate or revoke the key when the integration ends. Never put it in a
   repository, prompt, artifact, log, or generated file.

## What the plugin does

The Ara skill guides an agent to create and monitor bounded cloud coding
sessions through the documented REST API. It does not grant Ara access to the
local checkout or act on behalf of a user without an explicitly supplied,
scoped API key.

## Security

- The package contains no credential, authorization header, local hook, or
  executable setup step.
- API keys are supplied only by the calling service or CI secret manager; they
  are never stored or read by the package.
- Every API request must use the intended organization and the scopes required
  for that operation.
- Read [SECURITY.md](SECURITY.md) to report a vulnerability.

## Development verification

```sh
claude plugin validate --strict plugins/ara-claude
codex plugin marketplace add .
codex plugin add ara@ara
```

Use a temporary `HOME`/`CODEX_HOME` when testing the install commands so the
test does not alter your personal marketplace configuration.
