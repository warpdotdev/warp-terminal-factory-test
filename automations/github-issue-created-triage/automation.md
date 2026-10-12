---
triggers:
  - provider: github
    event: issue_created
    filter:
      repos:
        - warpdotdev/warp-stanford
---
A new issue was opened. Run the triage stage for it.

- If the issue author is a bot or automation account, stop silently.
- If the issue was created carrying the `auto-implement` label, skip triage and go directly to the implementation stage (this label is a trusted creation-time shortcut).
- Otherwise dispatch triage, render the triage summary body with `python3 skills/github/scripts/render-comment triage` (the comment structure - sections, the Maintainer details expando, disclaimer - is owned by the script; supply only the content JSON), post it, and apply the recommended labels. Issues that arrive already carrying lifecycle labels (for example, imported or re-opened issues) still get a fresh triage pass.
- When triage routes to an owning maintainer, assign them by running `python3 skills/github/scripts/assign-issue-owner <owner/repo> <issue> <owner>`; on exit 2 the routing mention is the fallback.
- Stop after triage. Do not spec or implement; those stages wait for the human gate labels.
