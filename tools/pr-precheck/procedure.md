# Procedure: how this skill grades a PR package

## Read order

1. Read the scope. In live mode, `scope.md` first: if its `Repo:` line is a placeholder, stop and tell the student to fill it. In eval mode, skip this step.
2. Read the issue context: title, body, thread highlights.
3. Read the repo facts. In eval mode, the bundle's repo-facts block. In live mode, the repo's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`. Note which sections the template names, which commands the contributing guide names, and whether any AI-disclosure requirement is stated.
4. Read the plan: its Files list, its Test-plan section, and its `## Deviations` section. This is the standard the diff is read against.
5. Read the diff. In live mode, `git diff main...HEAD`. In eval mode, the bundle's diff section. Note every changed file and every hunk.
6. Read the PR description last, with the diff still in mind.
7. Read the test evidence with the plan's Test-plan section still in mind.

## Evidence gathering

- **Plan fidelity:** pull the diff's changed-file list. Pull the plan's Files list and its `## Deviations` section. Record every file that appears in one and not the other.
- **Description consistency:** pull the description's claims about what changed. Compare each claim against the diff's hunks.
- **Test evidence:** pull the test evidence section and the plan's Test-plan section. Record specific commands or test ids and whether actual output appears next to them.
- **Repo checks:** pull the checks named in the repo's PR template and contributing guide. Record which appear in the evidence.
- **Diff quality:** read the diff line by line. Record debug prints, commented-out code, whitespace-only hunks, and any hunk the issue did not ask for.
- **Standards:** pull the PR template's sections. Record which the description fills in. Pull any AI-disclosure requirement from `docs/CONTRIBUTING.md`. If no requirement is stated, no disclosure is required.
- **Issue reference:** pull the issue number from the description. Any reference (`#N`, `Closes #N`, `Fixes #N`) counts.
- **Branch name:** in live mode, pull the branch name from the request. In eval mode, this check is unclear.
- **Honest shortfall:** compare the diff against the plan's Files list and Test-plan. If the diff does less than the plan promised, check that the description says so.

## Check execution

Run the checks in this order:

1. `diff_matches_plan`
2. `description_consistent_with_diff`
3. `test_evidence_observable`
4. `repo_checks_reported`
5. `no_debris_or_unrelated_hunks`
6. `standards_and_disclosure`
7. `honest_shortfall_disclosed`
8. `issue_referenced` (preferred)
9. `branch_name_convention` (preferred; unclear in eval mode)

If evidence for a check is genuinely absent, record it as `unclear`. The verdict rule treats unclear on a required check as fail and unclear on a preferred check as pass.

## Verdict assembly

1. Collect the grades for all checks.
2. If any required check is `fail` or `unclear`, the verdict is `reject`.
3. If every required check is `pass`, the verdict is `accept`.
4. In the JSON block, quote the deciding check's name and its evidence line verbatim.
5. In the readable summary above the block, name the voice-guide rules the PR title or description breaks (live mode only), then list each check's grade.