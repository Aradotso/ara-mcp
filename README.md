# Ara MCP

Official, secretless Ara MCP integrations for Codex and Claude Code. They connect to
Ara's remote MCP server at `https://api.ara.so/mcp/ara`; people sign in in their
browser and approve exactly one Ara workspace. The packages contain no API keys,
headers, local executables, hooks, or OAuth client secret.

## Install

### Codex

```sh
codex plugin marketplace add Aradotso/ara-mcp
codex plugin add ara@ara
```

Codex discovers Ara's OAuth metadata automatically. If it prompts you to sign
in, complete the Ara browser consent and choose the workspace the connection may
access.

### Claude Code

```sh
claude plugin marketplace add Aradotso/ara-mcp
claude plugin install ara@ara
```

Claude Code uses the same remote server and browser OAuth flow. It does not need
an Ara API key in your repository or local configuration.

## Headless CI

OAuth is for interactive clients. For an unattended job, create a minimally
scoped, organization-pinned `ara_` key in **Settings → Ara API**, put it only in
the job's secret manager, and call Ara's public REST API or MCP endpoint. Rotate
or revoke it when the job ends.

## What the plugin does

The Ara skill helps an agent create, monitor, and steer bounded Ara cloud coding
sessions, and use any explicitly granted public Ara API capability. That includes
write-only secrets and environment setup through `ara_api_request`. Ara work runs
in a separate cloud sandbox against a connected repository; it never silently
gains access to the local checkout.

## Security

- The package points only to Ara's HTTPS remote MCP endpoint.
- Workspace selection happens at Ara's OAuth consent screen, not in the plugin.
- Ara validates organization membership and the exact public API scope for every request.
- The API bridge only accepts a relative path in the approved workspace; secret
  values are write-only and never returned.
- The package never embeds a token, client secret, Authorization header, shell
  command, or local hook.
- Read [SECURITY.md](SECURITY.md) to report a vulnerability.

## Development verification

```sh
claude plugin validate --strict plugins/ara-claude
codex plugin marketplace add .
codex plugin add ara@ara
```

Use a temporary `HOME`/`CODEX_HOME` when testing the install commands so the
test does not alter your personal marketplace configuration.
