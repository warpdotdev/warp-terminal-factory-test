---
triggers:
  - provider: github
    event: issue_labeled
    filter:
      labels:
        - ready-to-spec
        - ready-to-implement
      repos:
        - warpdotdev/warp-stanford
---
A human applied `ready-to-spec` or `ready-to-implement` to an issue. The label itself requests factory work; no maintainer permission, assignment, or mention is required.

- Stop silently for bots or factory automation accounts (`warp`, `warp-factory`, `warp-agent`), including label echoes from verified mention requests. Preserve existing platform access checks.
- Select spec for `ready-to-spec`, implementation for `ready-to-implement`; when both are present implementation wins.
- Run `python3 skills/github/scripts/check-stage-gate <owner/repo> <issue> <spec|implement> --factory-login warp-factory --label-trigger --request-actor <event-actor-login>`. The helper verifies the latest matching human label event and current label. Only exit 0 authorizes dispatch; exits 1 and 2 stop. Do not run a maintainer-permission check or require factory assignment.
- Before implementation, inspect linked PRs and enforce current-revision human spec approval as described by the foreman. Pass existing spec and approval evidence to the helper; leave an implementation request pending if spec approval is missing. Reuse the spec PR/branch; report an existing implementation PR instead of opening a duplicate.
- Before spec work, inspect linked PRs and avoid duplicate specs.
- Do not post the former label-only announcement asking a maintainer to mention or assign the factory. Start the requested stage and report its result.
- Do not automatically assign the actor or anyone else. Preserve existing assignees and leave unassigned issues unassigned; named assignment changes remain maintainer-only.
