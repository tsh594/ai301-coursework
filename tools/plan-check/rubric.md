# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis_grounded | The plan's stated cause (its diagnosis section), read against the repro-evidence block | The stated cause is consistent with what the repro evidence actually shows. It does not contradict the traceback, the observed behavior, or the repro steps, and it does not introduce a cause the evidence cannot support. | required |
| target_matches_cause | The plan's described change, read against its stated cause | The change addresses the cause the diagnosis names, not merely the symptom the repro shows. A plan that patches a downstream call without addressing the named cause fails this check. | required |
| scope_bounded | The plan's scope statement: what is in scope, what is not, and the files or areas named | The plan names a bounded set of files, functions, or areas, states at least one thing it will not change, and does not describe an open-ended rewrite, refactor, or drive-by cleanup. | required |
| executable_by_stranger | The plan's approach and order-of-work sections | A stranger who has read only the plan and the repro evidence knows which file to open first, what to change, and in what order. No step depends on unstated context or asks the author a clarifying question. | required |
| test_plan_observable | The plan's test plan, read against the repro evidence's steps and artifacts | The test plan names observable checks (a specific test file, command, or output) that prove the fix, and re-runs the repro evidence's steps against the change with a stated expected result. "Tests pass" with nothing observable named fails this check. | required |
| unknowns_stated | The plan's risks and unknowns section, or its absence, read against the certainty of the plan's other claims | Uncertain things are stated as uncertain. A plan that presents an untested guess as certain fails. A plan that names no unknowns passes only if it also makes no claim that depends on one. | required |
| comment_respects_thread_and_repo | The plan comment, read against the thread highlights (maintainer signals) and the repo-facts block (templates, contributing asks, contribution policy) | The comment honors the thread's maintainer signals and the repo's stated comment and contribution conventions. It is a plan for this specific issue, not a generic template. | required |
| ai_disclosure_if_required | The repo-facts block of the package, read first, then the plan comment | Step 1: read the repo-facts block. Step 2: determine whether it contains an EXPLICIT, IMPERATIVE disclosure requirement — text that uses commands like "must", "required", or "shall" and specifically names AI assistance. General statements of AI policy, encouragement of transparency, bans on AI-generated code, descriptions of how the project uses AI, and any language that does not command contributors to disclose AI use do NOT count. Step 3: if an explicit imperative disclosure requirement exists and the plan comment does not carry its own explicit AI disclosure line, FAIL. Otherwise PASS. | required |

## Verdict rule

Accept the plan if every `required` check passes. Reject if any required check fails. `Unclear` on any required check counts as fail. `Preferred` checks never change the verdict; they refine the ranking among accepted packages only.