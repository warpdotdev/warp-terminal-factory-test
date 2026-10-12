---
triggers:
    - provider: github
      event: issue_mentioned
      filter:
        labels:
            - factory:warp-terminal-factory
        mentioned:
            - warp
            - warp-factory
        repos:
            - warpdotdev/warp-stanford
    - provider: github
      event: pull_request_mentioned
      filter:
        labels:
            - factory:warp-terminal-factory
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
        labels:
            - factory:warp-terminal-factory
        repos:
            - warpdotdev/warp-stanford
    - provider: github
      event: pull_request_assigned
      filter:
        assignees:
            - warp
            - warp-factory
        labels:
            - factory:warp-terminal-factory
        repos:
            - warpdotdev/warp-stanford
---

