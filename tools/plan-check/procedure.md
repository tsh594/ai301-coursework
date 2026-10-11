# Procedure: how this skill grades a plan package

## Read order

1. Read the issue context first: title, body, thread highlights. Note the behavior the issue describes and any maintainer signals (a stated preference for a minimal fix, a linked PR, a request to avoid a specific approach).
2. Read the repo-facts block next. Note the contribution policy, any explicit imperative AI-disclosure requirement, and any required comment or PR template.
3. In live mode, take the repo facts from the repo's own `README.md`,
   `docs/CONTRIBUTING.md`, and `.github/PULL_REQUEST_TEMPLATE.md`.
4. Read the repro-evidence block third. Note the traceback, observed behavior, exact steps, and environment details. This is the ground truth the diagnosis must be consistent with.
5. Read the plan's diagnosis section last, with the repro evidence still in mind.
6. Read the plan's scope, approach, test plan, and risks sections in that order.

Reading the repro evidence before the plan keeps the plan's claims from framing your read of the evidence.

## Evidence gathering

- **Diagnosis:** pull the plan's stated cause from its diagnosis section. Pull the behavior it must explain from the repro-evidence block's traceback and observed-behavior sections. Record both verbatim.
- **Target:** pull the plan's described change. Record which file, function, or call it modifies.
- **Scope:** pull the in-scope and not-in-scope statements and the files or areas named. Record whether the list is bounded or open-ended.
- **Executability:** pull the approach and order-of-work sections. Record whether the first concrete action is named.
- **Test plan:** pull the test plan. Pull the repro-evidence steps and expected outputs. Record which observable checks it names and whether it re-runs the repro steps.
- **Unknowns:** pull the risks and unknowns section, or note its absence.
- **Comms:** pull the plan comment, the thread highlights, and the repo-facts block. Record any maintainer signal and any stated template or disclosure requirement.
- **Maintainer signals (live mode):** a commenter counts as a maintainer
  only if GitHub's author-association field marks them as an owner,
  member, or collaborator on the repo. Student comments
  (author-association NONE or CONTRIBUTOR) are not maintainer signals.
- **Other students' PRs on the same issue (live mode):** do not treat an
  open PR from a student as a maintainer signal and do not use it to
  grade the comment. It is context, not a signal. The comment may
  acknowledge it but is not required to.
- **The student's own repro comment (live mode):** identify it by the
  student's login. If multiple comments on the thread are theirs,
  prefer the one that names the behavior the plan's diagnosis must
  explain.

## Check execution

Run the checks in this order:

1. `diagnosis_grounded` — if the cause contradicts the evidence, record the exact inconsistency.
2. `target_matches_cause` — read the described change against the stated cause.
3. `scope_bounded` — count the named files or areas; check for at least one out-of-scope item.
4. `executable_by_stranger` — read the approach as if you had no context beyond the plan and the evidence.
5. `test_plan_observable` — check that the test plan names a specific test, command, or output and re-runs the repro steps.
6. `unknowns_stated` — read the risks and unknowns section against the certainty of the plan's other claims.
7. `comment_respects_thread_and_repo` — read the comment against the thread highlights and the repo-facts block.
8. `ai_disclosure_if_required` — read the repo-facts block for an explicit imperative disclosure requirement, then check the comment. If no such requirement exists, pass.

If evidence for a check is genuinely absent from the package, record it as `unclear` for that check. Do not infer missing evidence. The verdict rule treats unclear on a required check as fail.

Once evidence for a check has been pulled in the gathering stage, that check may be graded without re-reading the whole package.

## Verdict assembly

1. Collect the grades for all checks.
2. If any required check is `fail` or `unclear`, the verdict is `reject`.
3. If every required check is `pass`, the verdict is `accept`. Preferred checks refine the ranking only; they never change the verdict.
4. In the output, quote the deciding check's name and its evidence line verbatim.