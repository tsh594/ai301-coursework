# Unit 1 — Issue Selection

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72

**Verdict output**

Both candidates are in the scoped repo (codepath/pathreview-ai301-fa26-s3). gh isn't installed here, so I gathered evidence from the GitHub REST API.

Repo-level evidence (shared by both): not archived, pushed_at 2026-09-16 (6 days ago), 5 recent commits on main all by Andrew Burke (Aburke225), most recent 2026-09-16 — active.

Ranked accepted candidates

1. #72 — verify_password raises UnknownHashError on malformed stored hashes — best fit
- not_advanced_tier — pass: labels are bug, good first issue, api, tier-1; no tier-3.
- active_repository — pass: archived: false, last push 2026-09-16.
- unclaimed_issue — pass: one claim comment from sseid4 (author_association: NONE, 2026-09-22) — a classmate, and the Path Review house rule says student claims don't block. No assignees.
- clear_problem_statement — pass: names the file (core/security.py), the wrong behavior (exception escapes), and the expected behavior (return False).
- tier_1_starter — pass · good_first_issue — pass · small_specific_scope — pass (two files, one function, one xfail marker) · low_friction_category — pass (bug, localized) · relevant_stack — pass (Python + pytest).
- Why it fits you: pure Python in your strongest language, two files, and the @pytest.mark.xfail marker (manifest H-05) gives an unambiguous done-signal — remove it and the test must go green. Estimated 1–2 hours, and reproducing the malformed-hash failure is exactly the debugging practice you said you want.

2. #47 — API docs don't include example curl commands
- not_advanced_tier — pass: labels good first issue, docs, tier-1.
- active_repository — pass: same repo facts.
- unclaimed_issue — pass: no comments, no assignees.
- clear_problem_statement — pass · tier_1_starter — pass · good_first_issue — pass · small_specific_scope — pass (one file, docs/API.md) · low_friction_category — pass (docs).
- relevant_stack — unclear: the deliverable is Markdown and curl invocations, none of your listed languages, though writing correct examples means reading the Python API code.
- Ranks second: uncontested and lower-risk, but the body doesn't bound how many endpoints need examples, and it's estimated 2–3 hours against #72's 1–2.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72",
    "checks": [
      {"name": "not_advanced_tier", "grade": "pass", "evidence": "Labels: bug, good first issue, api, tier-1 — no tier-3."},
      {"name": "active_repository", "grade": "pass", "evidence": "Repo archived:false, pushed_at 2026-09-16; 5 recent main commits by Aburke225."},
      {"name": "unclaimed_issue", "grade": "pass", "evidence": "Only claim is from sseid4 (author_association NONE, a classmate); Path Review house rule: student claims do not block. No assignees."},
      {"name": "clear_problem_statement", "grade": "pass", "evidence": "\"core/security.py lets passlib's UnknownHashError escape... should fail closed (return False), not raise.\""},
      {"name": "tier_1_starter", "grade": "pass", "evidence": "Carries the tier-1 label."},
      {"name": "good_first_issue", "grade": "pass", "evidence": "Carries the 'good first issue' label."},
      {"name": "small_specific_scope", "grade": "pass", "evidence": "Relevant files: core/security.py and tests/unit/test_security.py; one function, one xfail marker."},
      {"name": "low_friction_category", "grade": "pass", "evidence": "Labeled bug; fix is localized to one function plus its test."},
      {"name": "relevant_stack", "grade": "pass", "evidence": "Python with passlib and pytest — contributor is proficient in Python."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/47",
    "checks": [
      {"name": "not_advanced_tier", "grade": "pass", "evidence": "Labels: good first issue, docs, tier-1 — no tier-3."},
      {"name": "active_repository", "grade": "pass", "evidence": "Repo archived:false, pushed_at 2026-09-16; 5 recent main commits by Aburke225."},
      {"name": "unclaimed_issue", "grade": "pass", "evidence": "Comments endpoint returns none; assignees: none."},
      {"name": "clear_problem_statement", "grade": "pass", "evidence": "\"docs/API.md describes each endpoint but has no example invocations.\" — states what, where, and expected."},
      {"name": "tier_1_starter", "grade": "pass", "evidence": "Carries the tier-1 label."},
      {"name": "good_first_issue", "grade": "pass", "evidence": "Carries the 'good first issue' label."},
      {"name": "small_specific_scope", "grade": "pass", "evidence": "Relevant files: docs/API.md — a single named file."},
      {"name": "low_friction_category", "grade": "pass", "evidence": "Labeled docs; documentation-only change."},
      {"name": "relevant_stack", "grade": "unclear", "evidence": "Deliverable is Markdown plus curl commands, not Python/SQL/HTML/CSS/JS, though writing them requires reading the Python API."}
    ],
    "verdict": "accept"
  }
]
```

---

## Eval iterations

**Run history**

Score: 9/20 (full run, initial rubric — `eval-run.txt`)
Score: 16/20 (full run, revised rubric — `eval-run.txt`, final)

**Issue analysis**

issue-09 — my rubric rejected it; the gold label accepted it.

My rubric's `unclaimed_issue` required check fired because the bundle text showed a claim comment, so the issue was rejected. The gold label accepts it anyway. This is the Path Review house rule at work: in a classroom repo, a classmate's claim comment does not block you, and the issue remains a valid candidate. My rubric carried over the usual open-source convention that a claimed issue is taken, which is wrong for this repo. The gold label reflects the correct read.

**Check rationale**

`unclaimed_issue` — "The issue does not already have a claim comment from another contributor indicating active work. If someone has already said they are working on it, treat the issue as unavailable."

I wrote this check because on most open-source repos a claim comment means the issue is taken, and I wanted to avoid picking something another contributor had already started. It is a `required` check because on a normal repo, working on an already-claimed issue wastes everyone's time.

**Trade-offs**

This check costs me one scored issue (issue-09) that the gold label accepts, because Path Review explicitly does not honor claim comments the way a normal repo does — classmates' claims are ignored and everyone can work the same issue. The check is doing its job in the broader open-source sense, but it is calibrated for the wrong repo. If I kept iterating, I would either drop it to `preferred` or rewrite it to only fire on a maintainer's explicit assignment, not a peer's claim. I chose not to iterate further because the cost of another full eval run (~$4) outweighed one scored issue, and the assignment explicitly states the score is not what earns points.

---

## Selection rationale

**Selection rationale**

1. Fit to interests and time. Issue #72 is a pure-Python bug fix in `core/security.py`, which is my strongest language, and my interest in data analysis and backend work makes Python the natural fit. It is a tier-1 starter issue with a small scope (one function plus its test), estimated at 1–2 hours, which fits the small amount of time I had left in the grace period after being ill with a fever and cough.

2. What the verdict identified correctly, and what I weighed that the rubric could not. The rubric correctly identified #72 as tier-1, well-scoped, and tagged `good first issue`, and it accepted it. What I weighed that the rubric could not is that the issue ships with a `@pytest.mark.xfail` marker, which gives an unambiguous done-signal: when the fix lands, the test must go green. That clarity makes #72 a safer first contribution than the documentation issue, where the deliverable size is less well bounded. A previous documentation contribution of mine went poorly for exactly this reason — I wrote the ideal behavior instead of what the API actually returned, and lost points on "tests documented." Choosing a bug fix instead forces the verification step that was missing.

3. Anticipated difficulty in claiming it. The main difficulty will be reproducing the malformed-hash failure locally so I can prove the bug exists before fixing it, and understanding how `passlib` surfaces the `UnknownHashError`. Claiming is straightforward because it is a public tier-1 issue.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.