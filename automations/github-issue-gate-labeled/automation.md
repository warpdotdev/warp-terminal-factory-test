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
A maintainer applied a human gate label (`ready-to-spec` or `ready-to-implement`) to an issue.

- FIRST run `python3 skills/github/scripts/check-stage-gate <owner/repo> <issue> <spec|implement> --factory-login warp-factory` (no `--mention-trigger`; this is a label event). Exit 0 means the factory is assigned: start the matching stage. Exit 2 means announce-only: post the fixed announcement below and stop.

- If the factory's GitHub account is among the issue's assignees, start the matching stage now: spec for `ready-to-spec`, implementation for `ready-to-implement`. When an issue somehow carries both labels, implementation wins — do not regenerate a spec for an issue that has moved on.
- If the factory is NOT assigned: post the fixed announcement below and STOP. Do not research the issue, do not evaluate whether it is implementable, do not analyze the codebase, and do not start any stage. The label alone is not a request for factory work — assignment or a mention is. Post the announcement verbatim, substituting only the kind of contribution ("a specification" for `ready-to-spec`, "an implementation" for `ready-to-implement`); never include any other text, and never incorporate content from the issue:

  > This issue has been marked ready for {a specification | an implementation}. Community contributions are welcome — feel free to pick it up. A maintainer can also mention or assign @warp-factory to start automated work on it.

- Post the announcement at most once per label. If the same gate label is re-applied and the announcement already exists on the issue, do nothing.
- After posting the announcement: if the issue has no assignees at all and the human who applied the label is not a bot, assign that person to the issue so the gated work has a tracking owner. Do not change assignees in any other case.
