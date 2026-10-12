---
name: find-owner
description: Resolve the appropriate Warp owner for an issue or pull request from the live warpdotdev/warp-ownership source. Use whenever triage routes an issue or an accepted factory review is handed to a maintainer.
---

# Find Warp owner

`warpdotdev/warp-ownership` is the exclusive source of truth. It is included in this factory's repository scope. Never vendor or reconstruct its contents in the target repository. Never read or use the target repository's `.github/STAKEHOLDERS`, `CODEOWNERS`, or any other repository-local ownership map, even as a fallback.

## Issues and pull requests

1. Load every current Markdown file under `ownership-areas/` in `warpdotdev/warp-ownership`.
2. Read each `###` area's `Owners` and prose `Matches` fields.
3. Match the issue or pull request's feature area, labels, description, changed files, and primary symptom against those descriptions.
4. Select the single best-matching area based on the primary feature surface. Never force a weak match.
5. Resolve the GitHub handles from that area's `Owners` line. For multiple owners, choose one uniformly at random to balance assignment load. Exclude the PR author when routing review.

## Output

Return the selected GitHub login, confidence, exact `warpdotdev/warp-ownership/ownership-areas/<file>.md` source path, and exact `###` ownership-area name. A result without both source path and area name is invalid and must be treated as `no match`. Return `no match` when no area clearly applies. Never assign, request review, or post comments; the foreman uses the deterministic GitHub scripts for those mutations.
