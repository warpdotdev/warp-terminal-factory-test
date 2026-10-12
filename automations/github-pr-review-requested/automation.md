---
triggers:
  - provider: github
    event: pull_request_labeled
    filter:
      labels:
        - factory-review
      repos:
        - warpdotdev/warp-stanford
  - provider: github
    event: pull_request_review_requested
    filter:
      repos:
        - warpdotdev/warp-stanford
---
Someone explicitly requested a factory review on this pull request — via the `factory-review` label or a review request addressed to the factory's account.

- Apply the contributor-tier rules and the review budget (at most 5 factory review passes per PR per day for external contributors). When the budget is spent, say so briefly and stop.
- Dispatch the review stage on the current revision and post the resulting review on the PR.
- REQUIRED final step, not optional cleanup: if the trigger was the `factory-review` label, remove it from the PR immediately after the review is posted, so it can be re-applied to request another pass. The run is not complete until the label is removed; if removal fails, say so in a PR comment.
