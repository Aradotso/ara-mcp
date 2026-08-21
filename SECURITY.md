# Security policy

## Scope

This repository contains declarative marketplace packages and public API
integration guidance. It must remain secretless: do not add API keys, client
secrets, Authorization headers, cookies, local hooks, executable setup scripts,
or remote-tool configuration.

External integrations use the public REST API at `https://api.ara.so/v3` with a
least-privilege `ara_` API key kept only in the calling service or CI secret
manager.

## Reporting a vulnerability

Please report security issues privately at [security@ara.so](mailto:security@ara.so).
Do not include secrets, access tokens, or customer data in the initial report.

## Supported release

Only the default branch and the marketplace packages published from it are
supported. The packages never create or store user credentials; callers use a
scoped API key for explicitly requested service or CI integrations.
