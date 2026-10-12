---
triggers:
  - provider: github
    event: issue_mentioned
    filter:
      mentioned:
        - warp
        - warp-factory
      repos:
        - warpdotdev/warp-stanford
  - provider: github
    event: pull_request_mentioned
    filter:
      mentioned:
        - warp
        - warp-factory
      repos:
        - warpdotdev/warp-stanford
  - provider: github
    event: issue_assigned
    filter:
      assignees:
        - warp
        - warp-factory
      repos:
        - warpdotdev/warp-stanford
  - provider: github
    event: pull_request_assigned
    filter:
      assignees:
        - warp
        - warp-factory
      repos:
        - warpdotdev/warp-stanford
---
The factory was mentioned on, or assigned to, an issue or pull request.

- If the actor is a bot or a factory automation account (`warp`, `warp-factory`, `warp-agent`), stop silently. Keep existing platform access controls.
- Maintainer permission is required only for assignment changes or named reviewer requests, not stage requests or spec approval. Use `check-repo-maintainer` only for those protected operations and review-budget classification.
- Route explicit human requests before existing labels. On an issue, accept standalone `@warp spec this for me`, `@warp write a spec`, `@warp /spec`, `@warp implement this`, or `@warp /implement` (also `@warp-factory`). Verify the current comment id and actor with `check-stage-gate` using `--request-comment-id` and `--request-actor`. After verification, apply `ready-to-spec` or `ready-to-implement` and dispatch in this run. Never wait for factory assignment or maintainer approval. Label-write failures are reported, not grounds to reject an otherwise verified request. Ignore bot label echoes; inspect linked PRs to prevent duplicates.
- A quoted, fenced, negated, or ambiguous request is not authorization. Ask for a clear supported request and do not fall through to another stage. Spec and implementation requested together are ambiguous; approval plus implementation may use two standalone lines.
- With no explicit request, preserve existing labeled-issue handling: `ready-to-implement` selects implementation, otherwise `ready-to-spec` selects spec. An assignment with neither label does not authorize either stage.
- With no explicit request or ready label, run a (re-)triage pass. A bare mention never authorizes further work.
- On a pull request: treat the mention as a request for help on that PR — respond to the comment, address requested changes when the PR is factory-authored, or run a review pass when one is asked for. Apply the contributor-tier rules and the review budget before doing review work.
- Review commands: when the mentioning comment contains `/warp-agent-review` or `@warp-factory /review`, treat it as an explicit request for a fresh review pass on the current revision (same behavior as the `factory-review` label). Note: a bare slash command without an @warp-factory mention cannot trigger this automation; if you see one quoted in a thread you are already handling, honor it.
- Spec approval by mention: on a verified factory spec PR linked to a same-repo issue, any platform-authorized human can use `@warp /approve-spec`, `@warp spec approved`, or `@warp plan approved` (also `@warp-factory`). Run the helper's `approve-spec` stage with `--spec-pr <number> --request-comment-id <id> --request-actor <actor>`. On exit 0, record the approving actor, comment URL, and current spec head SHA; apply `plan-approved`, remove `ready-to-spec`, and perform approval bookkeeping. Bot label echoes must not repeat bookkeeping. No maintainer lookup is needed.
- On the factory spec PR, an explicit implementation request uses the linked issue and `--spec-pr`. An existing spec still requires current-revision human approval. If approval is missing, verify the request, apply `ready-to-implement` to the linked issue as pending, and report the approval blocker without dispatch. Approval plus implementation can be requested in one comment with separate `@warp /approve-spec` and `@warp /implement` lines. Approval alone starts implementation only if the linked issue already carries a pending `ready-to-implement` request; otherwise stop.
