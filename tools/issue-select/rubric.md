# Issue Selection Rubric

This rubric decides whether a Path Review issue is a good candidate for a
beginner open-source contributor. It favors small, well-scoped, tier-1
issues so the contributor can learn the contribution workflow without
getting stuck on a difficult technical problem.

## Checks

| Check | What it looks for | Weight | Type |
|-------|-------------------|--------|------|
| not_advanced_tier | The issue is NOT labeled `tier-3` (Advanced difficulty). Tier-3 issues require deep architectural work, multi-file changes, or complex integrations unsuitable for a first contribution. | 1 | required |
| active_repository | The issue's repository is active and maintained. Reject issues from archived, abandoned, or dead repositories — for example, repos with no recent commits, a notice that the project is unmaintained, or maintainers who do not respond. | 1 | required |
| unclaimed_issue | The issue does not already have a claim comment from another contributor indicating active work. If someone has already said they are working on it, treat the issue as unavailable. | 1 | required |
| clear_problem_statement | The issue description states what is wrong (or missing), where in the codebase it lives, and what the expected behavior should be. A reader should understand the problem without guessing. | 1 | preferred |
| tier_1_starter | The issue is labeled `tier-1` (Starter difficulty). These are the maintainers' strongest signal that the issue is suitable for a newcomer. | 2 | preferred |
| good_first_issue | The issue carries the `good first issue` or `beginner-friendly` label. | 1 | preferred |
| small_specific_scope | The issue names one or two specific files, functions, or lines rather than describing a system-wide change or an open-ended feature. | 1 | preferred |
| low_friction_category | The issue belongs to a low-friction category: documentation (`docs`), test coverage (`tests`), or a small localized `bug` fix. | 1 | preferred |
| relevant_stack | The issue involves Python, SQL, data analysis, HTML/CSS, or basic JavaScript — languages the contributor already uses. | 1 | preferred |

## Verdict rule

Accept the issue if **every** `required` check passes. Reject it otherwise.
The `preferred` checks only refine ranking within the accepted set; they
never change the verdict on their own.

## Notes

- Tier-1 issues are the primary target. Tier-2 issues are acceptable if
  they pass the required checks. Tier-3 issues are rejected by the first
  required check.
- Documentation-only, test-only, and small local bug fixes are the best
  candidates.
- Issues that are already claimed, from dead repos, or require deep
  architecture changes should be rejected.