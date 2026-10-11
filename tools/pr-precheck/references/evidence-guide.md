# Evidence guide: where evidence lives in a PR package

## Plan fidelity

**Where it lives:** In a bundle, the plan section's Files-to-change list and its `## Deviations` section, read against the diff section. In live mode, the student's `plan.md` and `git diff main...HEAD`.

**What good looks like:** Every file in the diff is named in the plan, or its addition is disclosed in `## Deviations` with a stated reason. The description's Changes list matches the diff in both directions: no change named in the description is missing from the diff, and no hunk in the diff is missing from the description.

## Test evidence

**Where it lives:** In a bundle, the test-evidence section. In live mode, the `test_evidence.md` file the request names.

**What good looks like:** Specific commands or test ids appear next to their actual output (a pass/fail line, a traceback, a count). Every check the repo's contributing guide names (`make test-unit`, `make test-integration`, `make lint`, `make typecheck`) is reported, or a skip is stated with a reason. An honest failure with a stated reason passes.

## Diff quality

**Where it lives:** The diff section, read line by line.

**What good looks like:** The diff contains only the fix. No `print()` debugging, no `console.log`, no commented-out code, no whitespace-only hunks, no files the issue did not name. One unrelated hunk fails this check even if the description explains it.

## Standards and comms

**Where it lives:** The PR description in a bundle, or the student's draft PR description in live mode, read against the repo's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`.

**What good looks like:** Every template section is filled in. The description names the issue with `Closes #<number>`. The Notes for Reviewers section states how AI was used on the change, in the student's own words. The branch name has a type prefix, the issue number, and a short slug.