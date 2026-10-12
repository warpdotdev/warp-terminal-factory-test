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
A maintainer applied the `plan-approved` label to a pull request. This marks the spec in that PR as approved.

Perform the approval bookkeeping, then decide whether implementation starts:

1. Post a short fixed comment on the PR confirming the spec is approved and naming the linked issue. Do not echo PR or issue content into it.
2. On the linked same-repo issue: remove the `ready-to-spec` label (the spec stage is complete) and post a one-line note that the spec was approved, linking the spec PR.
3. Implementation dispatch is still gated: start the implementation stage ONLY if the linked issue carries `ready-to-implement` AND the factory's account is assigned to it. If either is missing, stop after the bookkeeping and note in the issue comment that a maintainer can apply `ready-to-implement` and assign the factory to begin implementation.
4. Never apply `ready-to-implement` yourself, and never merge the spec PR — both remain human actions.
