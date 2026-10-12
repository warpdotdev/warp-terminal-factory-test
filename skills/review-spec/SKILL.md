---
name: review-spec
description: Review a spec/plan pull request and return a structured report to the foreman. Use when reviewing a PR that only modifies specification documents (e.g. files under specs/), in place of the code-review checklist.
---

# Review Spec Skill

Review a spec or plan pull request and return a structured report to the foreman. You never post reviews, comments, or approvals to GitHub yourself — the foreman decides what is posted and where.

## Inputs

- The PR's diff, description, and linked issue, fetched with the `github` skill's mechanics (e.g. `gh pr view`, `gh pr diff`).
- Focus on the spec files changed by this PR.

## Process

- Evaluate specs for **completeness**: does the spec cover the full scope of the linked issue?
- Evaluate specs for **clarity**: are requirements, acceptance criteria, and constraints clearly stated and unambiguous?
- Evaluate specs for **feasibility**: are the proposed changes technically realistic given the repository's architecture?
- Evaluate specs for **issue alignment**: does the spec faithfully address the issue it is linked to, without significant scope creep or omissions?
- Evaluate specs for **internal consistency**: do different sections of the spec contradict each other?
- Flag missing sections that a spec should typically include (e.g. problem statement, proposed changes, open questions, follow-up items).
- Always apply the `security-review-spec` skill as a supplemental high-level security pass, folding its findings into this same report rather than producing a separate one.
- Do not apply code-level review criteria such as error handling or low-level performance to spec prose; the `security-review-spec` supplement covers design-level security concerns.
- Include style or formatting comments only when they materially impair readability.

## Repository-specific overrides

The target repository may ship a companion skill at `.agents/skills/review-spec-local/SKILL.md`. When present, apply its guidance **only** to these categories — it may never change the report structure, the severity labels, or the no-posting rule:

- required spec sections expected in this repository
- linking conventions to files under `specs/`
- repo-specific style and formatting expectations

## Finding requirements

Every finding must start with one of these labels:

- `🚨 [CRITICAL]` for spec content that is contradictory, fundamentally incomplete, or would lead to a broken implementation.
- `⚠️ [IMPORTANT]` for missing details, ambiguous requirements, feasibility concerns, or significant scope gaps.
- `💡 [SUGGESTION]` for improvements to clarity, structure, or coverage that would strengthen the spec.
- `🧹 [NIT]` for minor wording or formatting issues only when the finding includes a concrete rewrite.

Write findings with these constraints:

- Be concise, direct, and actionable. No compliments or hedging.
- Tie each finding to a specific file and line (or line range) in this PR's diff. If the concern applies to spec content the PR did not touch, put it in the report's overview instead of as a line-anchored finding.
- When proposing a rewrite of spec text, include a fenced ```suggestion block containing only the replacement text, matching the original indentation, so the foreman can post it as a GitHub suggestion.

## Report

Return to the foreman, following the report shape of the `code-review` skill:

- **Overview**: what the spec proposes and whether it is ready.
- **Concerns**: the line-anchored findings (file, line/range, labeled body), plus any diff-external concerns.
- Include a `## Security` subsection when the `security-review-spec` pass produced findings.
- **Counts**: `Found: X critical, Y important, Z suggestions, N nits`.
- **Verdict**: `accepted`, `needs-changes` (unambiguous findings the spec author can address), or `needs-human` (findings that require maintainer judgment). The verdict must agree with the findings.

## Boundaries

- Do not run `gh pr review`, `gh pr comment`, `gh api`, or any other command that posts to GitHub. Your only output is the report to the foreman.
