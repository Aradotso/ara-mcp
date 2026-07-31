---
name: ara
description: Set up and operate an Ara workspace through the configured Ara MCP server, including repositories, environments, secrets, MCP servers, reusable skills, teammates, and cloud coding sessions.
---

# Ara workspace

Use Ara's native MCP tools to prepare the selected workspace and run coding
sessions in isolated cloud sandboxes. Prefer the typed Ara tools. Use
`ara_api_request` only when no typed tool covers an explicitly requested public
operation.

## Authentication and authority

The remote server is `https://api.ara.so/mcp/ara`. Interactive use completes
Ara OAuth in a browser and binds the connection to exactly one approved
workspace. Never ask for, print, store, or add an Ara API key to a project file.
An API key is appropriate only for an explicitly requested unattended CI or
headless job and belongs solely in that job's secret manager.

The available tool catalog is the current principal's authority. A missing tool
usually means the OAuth grant lacks its required capability. Ask the user to
reconnect Ara and approve the additional capability; do not work around a
missing tool or scope with another credential.

## Read before writing

For codebase setup, first inspect the selected workspace without mutating it:

1. Use `ara_repository_manage` to list connected repositories and confirm the
   target provider and `owner/repo`.
2. Use `ara_environment_manage` to read the current Recipe, resolved
   session-start script, runbook, and relevant build state.
3. Use `ara_secret_manage` to list configured secret names only. Secret values
   are write-only and are never returned.
4. Use `ara_plugin_list` and `ara_mcp_server_manage` to inspect native plugin
   state, the MCP catalog, and existing organization MCP-server registrations.
5. Use `ara_skill_manage` to inspect the workspace's reusable team skills.

Preserve useful existing configuration. Recipe, runbook, and similar writes may
replace shared state, so always read their latest value immediately before a
write.

## Codebase setup

Configure only what the user requested:

1. Connect the target repository with `ara_repository_manage` only if it is not
   already connected. The workspace's GitHub or GitLab provider must already be
   authorized; if it is not, ask the user to finish that provider's browser
   authorization, then continue.
2. Discover the repository's required environment variable names, setup
   commands, run/test commands, and MCP configuration from its files. Treat
   local `.env` files, MCP headers, tokens, and credential stores as sensitive.
3. Before uploading any discovered secret, show the user the secret names,
   target repository, and destination scope, and obtain approval unless their
   request already explicitly authorized those exact uploads. Send approved
   values only through `ara_secret_manage`; never repeat them in a prompt,
   response, runbook, skill, Recipe, or log.
4. Configure organization MCP servers through `ara_mcp_server_manage`:
   - No-auth HTTP/SSE servers still need a real reachability check.
   - Bearer or API-key credentials belong in the tool's write-only `secrets`
     field, with the same explicit approval as repository secrets.
   - Selecting `auth_type: oauth` registers the server but does not authorize a
     third-party account. The user must complete that provider's OAuth flow in
     Ara before a session can use it.
   - A stdio server is usable in Ara only when its command and package are
     portable to the cloud sandbox. Local absolute paths, desktop sockets, and
     machine-specific commands are not portable.
5. Store non-secret recurring setup in Recipe YAML through
   `ara_environment_manage`. Put checkout-dependent setup in `session_start`.
   Build and pin an image only when the reusable environment genuinely needs
   it.
6. Replace the runbook only after reading it. Record verified install, run,
   test, and operating commands plus unresolved gaps, without secret values.
7. Upload reusable operating guidance through `ara_skill_manage`. Keep it
   narrowly scoped, give it clear activation language, and enable it only when
   future Ara sessions should receive it.
8. Use `ara_team_manage` only when the user explicitly requests the exact
   invitation, removal, or role change.

## Prove the setup

Configuration alone is not completion. Start a fresh acceptance session with
`ara_session_create` after the writes, because an already-running session may
not receive new secrets, Recipe content, MCP servers, runbook content, or
skills. Ask the new session to:

- verify required environment variables are present without printing values;
- run a harmless install, test, or repository command;
- explicitly invoke any newly added skill;
- call one harmless read-only operation on each newly configured MCP server;
- report concrete evidence and every remaining gap.

Monitor it with `ara_session_events` and steer it with
`ara_session_interact` when needed. Ara's public terminal statuses are `exit`
(completed), `error` (failed), and `suspended` (cancelled or quota-limited).
Do not declare setup complete until the fresh session reaches a terminal state
and its evidence proves the configured capabilities work.

## Bounded cloud work

1. Confirm the task and connected `owner/repo`.
2. Use `ara_session_create` with acceptance criteria, relevant files, expected
   tests, and whether a pull request is expected.
3. Return the session URL promptly; Ara's sandbox is separate from the local
   checkout.
4. Use `ara_session_events` to inspect progress and artifacts, and
   `ara_session_interact` to clarify or steer the work.
5. Report the final status and pull-request or artifact links. Never claim work
   landed without checking the returned session state.

## Guardrails

- Only create sessions or automations the user requested.
- Never disconnect a repository, delete configuration, remove a member, change
  a role, or send an invitation without authorization for that exact mutation.
- Never copy a user or Ara credential into an Ara run, repository file, prompt,
  response, runbook, skill, Recipe, or artifact.
- For temporary verification changes, snapshot shared state first, restore it
  afterward, and verify the restoration.
