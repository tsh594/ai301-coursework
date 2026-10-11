# Rubric: is this pull request ready to submit?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diff_matches_plan | The diff's changed-file list, read against the plan's stated Files and its `## Deviations` section | Every file the diff changes is named in the plan's Files list, or disclosed in `## Deviations` with a reason. A file that is neither named nor disclosed fails this check. | required |
| description_consistent_with_diff | The description's claims about what the change does, read against the diff | No description claim contradicts what the diff actually does. A description that names a change the diff does not make fails. A description that omits a supporting file (a test, a CHANGELOG entry) does not fail. | required |
| test_evidence_observable | The test evidence section, read against the plan's Test-plan section | The evidence names at least one specific command, test id, or script and shows its actual output (a pass/fail line, a traceback, a count). "Tests pass" with no output shown fails. An honest failure that the description discloses passes. | required |
| repo_checks_reported | The test evidence section, read against any checks the repo's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` explicitly name as commands | If the repo names specific commands or checks (for example `make test-unit`, `make lint`), the evidence reports each one's outcome or states a skip with a reason. If the repo names no specific commands, this check passes. | required |
| no_debris_or_unrelated_hunks | The diff read line by line | No debug prints, no commented-out code, no stray console logs, no whitespace-only hunks, no unrelated cleanup. One unrelated hunk fails this check. | required |
| standards_and_disclosure | The description, read against the repo's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` | Every section the repo's PR template names is filled in. If the repo's contributing guide states an AI-disclosure requirement, the description discloses AI use; if no such requirement exists, this part passes without a disclosure. | required |
| honest_shortfall_disclosed | The description's Notes for Reviewers section, read against the diff and the plan's `## Deviations` | If the diff does less than the plan promised, or the fix needed a change the plan did not name, the description states so with a reason. If the diff matches the plan, this check passes. | required |
| issue_referenced | The description, read against the issue the plan belongs to | The description references the issue number (any of "#N", "Closes #N", "Fixes #N", or equivalent). A description that names no issue number fails. | preferred |
| branch_name_convention | The branch name from the live-mode request, read against the repo's `docs/CONTRIBUTING.md` | The branch name follows `<type>/<issue-number>-<slug>`. In eval mode there is no branch name; grade this check `unclear`, and the verdict rule treats it as pass. | preferred |

## Verdict rule

Accept the PR if every `required` check passes. Reject if any required check fails. `Unclear` on a required check counts as fail. `Unclear` on a preferred check counts as pass. `Preferred` checks never change the verdict; they refine the ranking among accepted packages only.