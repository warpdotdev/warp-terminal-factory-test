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
- For a PR from an external contributor, additionally check its linked same-repo issue(s) for the `ready-to-implement` (or `ready-to-spec` for spec-only PRs) gate label:
  - If no linked issue carries the required gate label, include a short fixed workflow note in the review body — the PR needs a linked issue marked by a maintainer before it can merge — and weigh this toward a changes-requested verdict. This note is IN ADDITION to the full review findings, never a replacement for them.
- Post the resulting review on the PR.
- Escalation is script-gated: after posting an accepted review, resolve the owner from live `warpdotdev/warp-ownership` with the `find-owner` skill and run `python3 skills/github/scripts/request-owner-review <owner/repo> <pr> --bot warp-factories --owner <owner>`. The script requests that maintainer's review only when the posted verdict is accepted, and refuses (exit 2) otherwise — obey it; never request a reviewer any other way.
- Apply the daily review budget for external contributors (at most 5 factory review passes per PR per day).
- Never merge, approve for merge, or close a PR. The review is advisory; merging is a human decision.
