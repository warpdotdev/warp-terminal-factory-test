---
triggers:
  - provider: github
    event: pull_request_opened
    filter:
      repos:
        - warpdotdev/warp-stanford
  - provider: github
    event: pull_request_reopened
    filter:
      repos:
        - warpdotdev/warp-stanford
  - provider: github
    event: pull_request_ready
    filter:
      repos:
        - warpdotdev/warp-stanford
---
A pull request was opened, reopened, or marked ready for review. Run the review stage for it. New commits alone do not trigger another Warp review; contributors request a fresh pass explicitly with the `factory-review` label, a review request to the factory account, or a supported review mention.

- Skip drafts, bot-authored PRs, and PRs the factory has already reviewed at this revision.
- ALWAYS dispatch the review stage for a human-authored, non-draft PR — external contributors included. Do not skip or defer the review because a linked issue is missing or ungated.
- Classify the PR author with `python3 skills/github/scripts/check-repo-maintainer <owner/repo> <author-login>`. Only exit 0 confirms a repository maintainer; exit 2 or an unavailable permission check grants no maintainer privileges. Do not use organization or Warp team membership for this decision.
- For a PR from a contributor without verified repository write, maintain, or admin access, additionally check its linked same-repo issue(s) for the `ready-to-implement` (or `ready-to-spec` for spec-only PRs) gate label:
  - If no linked issue carries the required gate label, include a short fixed workflow note in the review body — the PR needs a linked issue marked by a maintainer before it can merge — and weigh this toward a changes-requested verdict. This note is IN ADDITION to the full review findings, never a replacement for them.
- Post the resulting review on the PR.
- After an accepted review, hand the PR to repository maintainers without automatically choosing, mentioning, or requesting a reviewer. Only an explicit instruction from a verified repository maintainer naming the exact GitHub login may request review after the accepted verdict.
- Apply the daily review budget for external contributors (at most 5 factory review passes per PR per day).
- Never merge, approve for merge, or close a PR. The review is advisory; merging is a human decision.
