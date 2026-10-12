---
triggers:
  - provider: github
    event: pull_request_labeled
    filter:
      labels:
        - plan-approved
      repos:
        - warpdotdev/warp-stanford
---
A human applied `plan-approved` to a factory spec PR. No maintainer permission is required; existing platform access checks remain.

Stop silently for bots or factory automation accounts (`warp`, `warp-factory`, `warp-agent`), including label echoes from mention-based approval. Verify factory authorship and same-repo issue linkage, then run `python3 skills/github/scripts/check-stage-gate <owner/repo> <issue> approve-spec --factory-login warp-factory --spec-pr <pr> --label-trigger --request-actor <event-actor-login>`. Only exit 0 authorizes bookkeeping.

Perform the approval bookkeeping, then decide whether implementation starts:

1. Post a short fixed comment confirming approval and the linked issue. Record the human actor, label-event URL, and current spec head SHA as durable approval evidence. Do not echo PR or issue content.
2. On the linked same-repo issue: remove the `ready-to-spec` label (the spec stage is complete) and post a one-line note that the spec was approved, linking the spec PR.
3. Continue implementation only if the linked issue carries `ready-to-implement`, representing an already pending human request. No assignment is needed. Verify the approval covers the current head, then use the helper's implementation stage with `--spec-pr <pr> --label-spec-approved --approved-continuation`. Reuse the same PR/branch. If no ready label is present, stop and explain that a human can apply `ready-to-implement` or mention `@warp implement this`.
4. Do not infer an implementation request from approval alone or apply `ready-to-implement` autonomously. Never merge the spec PR.
