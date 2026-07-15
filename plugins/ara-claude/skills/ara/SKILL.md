---
name: ara
description: Create, monitor, or steer an Ara cloud coding session through the configured Ara MCP server. Use when the user asks to offload bounded implementation, debugging, review, or investigation work to Ara while local work continues.
---

# Ara

Ara sessions run in their own cloud sandbox against a connected repository. Use
the configured Ara MCP tools for this workflow, not raw REST calls or a local
shell wrapper.

## Authentication

The remote server is `https://api.ara.so/mcp/ara`. Interactive use completes
Ara OAuth in a browser and asks the person to approve exactly one workspace. Do
not ask for, print, store, or add an Ara API key to a project file. An API key
is only appropriate for an explicitly requested, unattended CI/headless job
and belongs solely in that job's secret manager.

## Offload flow

1. Confirm the task is bounded and identify the connected `owner/repo`.
2. Use `ara_session_create` with a concise prompt and the repository. Mention
   acceptance criteria, relevant files, test expectations, and whether a PR is
   expected.
3. Return the session URL immediately. Keep local work independent; Ara's
   sandbox is separate from the current checkout.
4. Use `ara_session_events` to inspect progress or artifacts, and
   `ara_session_interact` to clarify scope or steer the run.
5. Report the final status and PR/artifact links. Never claim a task landed
   without checking the returned session state.

## Guardrails

- Only create sessions and automations the user explicitly asked for.
- A tool absent from the MCP list is not authorized for the current principal;
  do not work around a missing scope.
- Use `ara_api_request` only for an explicitly requested public API action. Its
  path is relative to the approved workspace; never try to supply another org,
  a full URL, or a secret value in a prompt or log.
- Ara session credentials are scoped by the Ara service. Do not copy a user
  credential into an Ara run, repository secret, prompt, or artifact.
