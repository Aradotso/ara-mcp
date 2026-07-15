# Security policy

## Scope

This repository contains declarative marketplace packages and workflow guidance.
It must remain secretless: do not add API keys, OAuth client secrets,
Authorization headers, cookies, local hooks, or executable setup scripts.

The packages may point only to Ara's HTTPS public MCP endpoint:
`https://api.ara.so/mcp/ara`.

## Reporting a vulnerability

Please report security issues privately at [security@ara.so](mailto:security@ara.so).
Do not include secrets, access tokens, or customer data in the initial report.

## Supported release

Only the default branch and the marketplace packages published from it are
supported. A client must complete Ara OAuth in a browser; API keys are only for
explicit, unattended integrations that use a secret manager.
