---
triggers:
    - provider: github
      event: issue_labeled
      filter:
        labels:
            - factory:warp-terminal-factory
        repos:
            - warpdotdev/warp-stanford
---

