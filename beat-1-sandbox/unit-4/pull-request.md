# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request opened for #72, and of the evaluation runs that produced `eval-run.txt`.

---

## Your identity upstream

**GitHub username**

tsh594

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/139

**Branch**

fix/72-verify-password-fail-closed

---

## Eval iterations

**Run history**

Score: 3/3 (smoke run, --limit 3, not saved)
Score: 19/20 (full run, saved to eval-run.txt, bar PASS)

The last score matches the agreement line in the committed `eval-run.txt`:

```
agreement: 19/20 scored items  (bar: 18/20: PASS)
```

**Package analysis**

pkg-11 — my rubric rejected it; the gold label accepted it.

The required check `description_consistent_with_diff` fired, and the preferred check `branch_name_convention` also fired. `description_consistent_with_diff` is the one that decided the verdict: my rubric reads the PR description's claims against the diff and fails the package if the description names a change the diff does not make. In pkg-11 the description and the diff do not line up perfectly — the description makes a claim the diff does not fully support — so my rubric held the package. The gold label accepts it, which means the eval set treats that gap as small enough to pass. My check is written to fail on any contradiction; the eval set is written to accept a description that is not exhaustively precise about which hunks the change adds.

`branch_name_convention` is preferred, and in eval mode there is no branch name, so by my rubric's own rule it should grade `unclear` and pass. The model graded it `fail` instead. That points to a wording gap in the check's eval-mode instruction that I did not catch before the final run.

**Check rationale**

`description_consistent_with_diff` — "The description's claims about what the change does, read against the diff | No description claim contradicts what the diff actually does. A description that names a change the diff does not make fails. A description that omits a supporting file (a test, a CHANGELOG entry) does not fail. | required"

I wrote this check to separate real drift from completeness gaps. A description that names a change the diff does not make is a claim contradicted by the artifact, which is the silent-drift failure mode the rubric exists to catch. A description that omits a supporting file is a different thing — a report that could be more complete — and should not block the PR by itself. The first form I considered was stricter: "the description must enumerate every file in the diff." That version would have failed pkg-11 and several other accept-labeled packages whose descriptions are prose rather than exhaustive lists, so I chose the narrower no-contradiction form instead.

**Trade-offs**

This check gives up strict completeness. A description that omits a meaningful change entirely — an unrelated hunk the author forgot to mention — passes this check as long as it does not contradict the diff. Catching that case needs the stricter enumerated-change-list form I rejected above, because that form over-fires on prose descriptions and produced more false rejects than it fixed. The trade is: accept a small completeness gap to avoid failing packages whose descriptions are accurate but not exhaustive. On the final run this is exactly the trade that let pkg-11 through to a correct accept; the price is that a genuinely silent omission in a future package would also pass. I would catch that kind of omission with a separate check rather than by tightening this one, because the two failure modes are different and one check cannot cover both without failing one of them.

---

Related paths: `eval-run.txt` in this directory; your skill's files in `tools/pr-precheck/`.