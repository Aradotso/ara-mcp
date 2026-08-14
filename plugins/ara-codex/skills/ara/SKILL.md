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
   expected. Pass `model` and/or `reasoning_effort` to override the
   organization default for this session.
3. Return the session URL immediately. Keep local work independent; Ara's
   sandbox is separate from the current checkout.
4. Use `ara_session_events` to inspect progress or artifacts, and
   `ara_session_send` to clarify scope or steer the run. If a session stalls
   or fails before doing real work, resend with `model` and/or
   `reasoning_effort` set to retry on a different model rather than only
   reporting the failure; this starts a new continuation run, not an in-place
   retry.
5. Report the final status and PR/artifact links. Never claim a task landed
   without checking the returned session state.

## Pull-request iteration

- Use `ara_pr_get` as the indexed snapshot for the live run's pull or merge
  request. It includes identity/state, merge readiness and requirements, commits,
  paged changed-file patches, every check, formal reviews, inline comments,
  resolved/unresolved review threads, discussion, reviewer/assignee/label options,
  visual evidence, supported mutations, merge operation/blocker state, and
  explicit per-section completeness. Follow every file and feedback next offset
  until null. When a review thread has a comments next offset, pass its id as
  `review_thread_id` and page `thread_comment_offset` until null. Never treat
  an unavailable or provider-incomplete section as an empty one.
- When a check fails, call `ara_pr_check_get` with its `check_run_id` or exact
  name from `ara_pr_get`. It returns annotations, GitHub Actions failed steps,
  and a bounded redacted job-log tail when the provider exposes one.
- Re-read `ara_pr_get` after pushing a repair. Iterate until the relevant checks
  pass and all actionable review feedback is resolved; never weaken a check to
  manufacture a green result.
- Use `ara_pr_feedback_update` to post a summary, reply to inline feedback,
  react to discussion, and resolve or reopen review threads. Use `ara_pr_update`
  for title/body, draft/ready/close, base, reviewer, assignee, and label changes;
  pass the snapshot's head SHA as `expected_head_sha` when changing state.

## Guardrails

- Only create sessions and automations the user explicitly asked for.
- A tool absent from the MCP list is not authorized for the current principal;
  do not work around a missing scope.
- Use `ara_api_request` only for an explicitly requested public API action. Its
  path is relative to the approved workspace; never try to supply another org,
  a full URL, or a secret value in a prompt or log.
- Ara session credentials are scoped by the Ara service. Do not copy a user
  credential into an Ara run, repository secret, prompt, or artifact.
