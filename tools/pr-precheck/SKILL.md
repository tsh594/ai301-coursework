---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: **is this ready to submit?**

A PR package is a candidate pull request read against the plan it claims to implement and the issue that plan belongs to. It contains: a PR title, a PR description, a branch's committed diff, and test evidence. It is read against: the plan (with its Deviations), the issue's thread, and the repo's PR template and `docs/CONTRIBUTING.md`.

Do not answer any other question. Do not grade more than one package per run. Do not answer from gut feel: execute the components in this directory and report what they find.

## Inputs and modes

Two modes, defined here. Pick the mode the request names; if the request is silent, use live mode when an issue URL is given and eval mode when a bundle id is given.

**Live mode.** The student's own submission, checked before it goes out. Inputs:

- `plan.md` (with its `## Deviations` notes) — from the student's fork clone.
- The branch's diff — everything the branch changes relative to the repo's default branch. Produce it with `git diff main...HEAD` (three dots) from the top folder of the fork clone.
- The draft PR title and description — from the file the command names, or the argument.
- The test evidence — from the file the command names.
- The issue — the GitHub issue URL the command names. Fetch the thread, the repo's `.github/PULL_REQUEST_TEMPLATE.md`, and `docs/CONTRIBUTING.md`.

A house-chain student reads the house plan and the house repro pack in place of their own `plan.md` and their own evidence, and the same checks grade the same things.

**Eval mode.** The bundle is the whole world. Every fact comes from the bundle text: the plan, the repro evidence, the diff, the description, the test evidence, and the repo-facts block. Nothing is fetched. Nothing else is read. Every check in `rubric.md` runs, and the full verdict rule applies.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. It names where the student's PR must live and the house rules of that environment.

- If the `Repo:` line still carries an unfilled bracketed placeholder, stop without grading and tell the student to fill the `Repo:` line in `scope.md` with their section's Path Review repo. Do not guess a scope.
- If the student's PR is outside the scoped repo, refuse to grade and say why.
- Apply the house rules the scope names (for Path Review: classmate claims do not block, each student opens their own PR, credit attaches to the work).

Eval mode ignores `scope.md` entirely.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md`. Hold the outgoing PR text, the title and the description, against the rules it names. Report any rule the draft breaks in the readable summary above the JSON block.

The voice guide never changes the verdict on its own unless a rubric check reads it. Eval mode ignores `voice-guide.md` entirely: voice is personal and carries no gold labels.

## Component reads

- `rubric.md` defines the checks and the verdict rule. Read it first. Execute exactly the checks it names, in the order `procedure.md` gives.
- `references/evidence-guide.md` maps where each evidence family lives in a PR package. Use it to locate the fact each check names. If a check's evidence cannot be located, the check's grade is `unclear`.
- `procedure.md` is executed as written: read order, evidence gathering, check execution, verdict assembly. If the procedure is silent on a step, report the gap in the summary and stop. Never improvise around it.
- If `rubric.md` or `procedure.md` has no written content (a shipped template with only instruction comments counts as no content), refuse to grade and say so. An empty tool that invents checks at runtime is worse than no tool.

## Verdict and output

Verdict space is binary: `accept` (ready to submit) or `reject` (hold). No third verdict. Reservations belong in check evidence lines, not in the verdict.

End the reply with the fenced JSON block below, valid and last. Nothing follows it.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- **Evidence first.** No check is graded without naming the fact or quote that decided it. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** A terse complete PR can be ready; a beautiful confident one can hide drift. Read the artifact itself against the plan, the issue, and the stated standards. Never grade formatting.
- **The rubric decides.** If a check passes by its stated condition but feels wrong, it still passes. The fix belongs in the rubric, not in the run.
- **The procedure decides how.** Follow `procedure.md` as written. Report gaps; do not silently invent steps.
- **Unclear defaults to fail.** Treat `unclear` as the rubric's verdict rule directs. Where the rule is silent, an unverifiable claim is a failing one. A PR you cannot verify from the package is not ready to submit.