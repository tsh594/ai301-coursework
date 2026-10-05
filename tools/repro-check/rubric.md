# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| environment_recorded | The environment section of the repro report | The report records enough environment detail for a stranger to set up the same runtime and dependencies the issue depends on. Naming the runtime or language version is required. The OS, package manager version, and project commit are encouraged but not required unless the issue depends on them. Where the issue names a target environment, the report either matches it or explicitly calls out the difference. | required |
| steps_followable | The reproduction steps section of the repro report | The steps start from a clean checkout or a clearly stated starting state and end at the observed behavior. Every command, environment variable, file path, and expected intermediate result is named inside the step. Steps that reference undefined variables, assume an unstated tool, or jump without showing the intermediate state fail this check. | required |
| behavior_matches_issue | The output excerpt, log, or screenshot in the report, read against the error the issue describes | PASS if the artifact shows a failure in the same function, module, or code path the issue describes. PASS if the artifact shows the same error class (e.g. an exception on the same handler) even with different wording. Only FAIL if the artifact clearly demonstrates a completely different feature or an unrelated error that has nothing to do with the issue's description. When in doubt, PASS. | required |
| outcome_honest | The stated outcome in the report, read against the report's own artifacts | The stated outcome is exactly what the evidence shows. An evidenced cannot-reproduce is a pass. A confident claim that goes beyond the artifact (a fix with no diff, a cause with no evidence) is a fail. | required |
| ai_disclosure_if_required | The repo-facts block of the package, read first, then the claim comment and repro comment | Step 1: read the repo-facts block. Step 2: determine whether it states any policy, rule, or requirement about disclosing AI assistance when contributing or commenting. Step 3: if such a policy exists, then check whether BOTH the claim comment AND the repro comment contain an explicit AI disclosure statement of their own (phrases like "AI-assisted", "prepared with AI assistance", "used AI", or equivalent). If a disclosure policy exists and either comment does not carry its own disclosure statement, FAIL. If no such policy exists, PASS. Do not infer disclosure from elsewhere; the comment itself must show it. | required |
| conventions_respected | The claim comment and the repro comment, read against the repo's stated template asks in the repo-facts block | If the repo-facts block names a required comment template or required sections, the comments must honor those substantive asks. Boilerplate that ignores a named template ask fails this check. If no template is named, PASS. | required |
| issue_specific | The claim comment and the repro comment, read against the issue's description | The comments name the specific function, file, and error from the issue, not a generic template. | preferred |

## Verdict rule

Accept the package if every `required` check passes. Reject if any
`required` check fails. `Unclear` on any required check counts as fail.
`Preferred` checks never change the verdict; they refine the ranking
among accepted packages only.