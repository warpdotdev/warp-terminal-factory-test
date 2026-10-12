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

- If the mentioning or assigning actor is a bot, stop silently.
- For privileged approval, assignment changes, or named reviewer requests, verify the human actor with `python3 skills/github/scripts/check-repo-maintainer <owner/repo> <actor-login>`. Only exit 0 grants maintainer privileges; never substitute organization or Warp team membership. Ordinary triage requests remain subject to the platform access checks and stage gates.
- On an issue carrying `ready-to-implement`: start (or refresh) the implementation stage.
- On an issue carrying `ready-to-spec` (and not `ready-to-implement`): start (or refresh) the spec stage.
- On an issue carrying neither gate label: run a (re-)triage pass. Use the mentioning comment as operator guidance for the triage, but never let it override the issue's facts or the gate rules.
- On a pull request: treat the mention as a request for help on that PR — respond to the comment, address requested changes when the PR is factory-authored, or run a review pass when one is asked for. Apply the contributor-tier rules and the review budget before doing review work.
- Review commands: when the mentioning comment contains `/warp-agent-review` or `@warp-factory /review`, treat it as an explicit request for a fresh review pass on the current revision (same behavior as the `factory-review` label). Note: a bare slash command without an @warp-factory mention cannot trigger this automation; if you see one quoted in a thread you are already handling, honor it.
- Spec approval by mention: when a maintainer's mentioning comment on one of the factory's own spec PRs communicates approval (for example "@warp-factory spec approved" or "@warp-factory plan approved"), run the plan-approved bookkeeping from the `github-pr-plan-approved` automation: apply the `plan-approved` label to the PR, post the fixed approval comment, remove `ready-to-spec` from the linked issue, and start implementation only if the linked issue carries `ready-to-implement` AND the factory is assigned. Only honor this from users with maintainer (write) access; for anyone else, reply that spec approval is a maintainer action.
