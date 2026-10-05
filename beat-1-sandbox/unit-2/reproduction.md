# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`.

---

## Your identity upstream

**GitHub username**

tsh594

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5987714932

I'd like to work on this issue. I'll reproduce the `UnknownHashError` raised by `verify_password` in `core/security.py` when it is handed a malformed stored hash, and I'll post a reproduction report with my environment, the exact steps I ran, and the observed output.

I plan to start from the xfail test in `tests/unit/test_security.py` (manifest id H-05), since that test looks like the reproduction path the issue is pointing at. I'll remove nothing and change nothing until I can show the failure in my own environment.

I'm not promising a fix or a date. The report comes first, and it will say what actually happens on my machine.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5988133762

Reproduction report for #72. Result: reproduced on commit 2f4e82f. The test `test_verify_with_wrong_hash_format` raises `UnknownHashError` from `verify_password` instead of returning `False`.

One environment note: I ran this on Python 3.14.7 on my Windows machine. The bug is in how passlib handles a hash string it does not recognize, and that behavior is not Python-version-specific, so this should reproduce on the 3.11 the project targets as well.

## Environment

- **OS:** Windows 11 (Python reports platform as `win32` on all Windows builds, 32-bit or 64-bit)
- **Python:** 3.14.7
- **passlib:** 1.7.4
- **bcrypt:** 4.3.0
- **pytest:** 9.1.1
- **Repository:** https://github.com/tsh594/pathreview-ai301-fa26-s3
- **Commit:** 2f4e82f52efbcfcc57d65b3fa5348672163ca088

## Steps to reproduce

All commands run in **Git Bash** (the shell `docs/SETUP.md` requires on Windows).

1. Clone the fork, enter the repo, and pin the commit this report was tested on:
   ```
   git clone https://github.com/tsh594/pathreview-ai301-fa26-s3.git
   cd pathreview-ai301-fa26-s3
   git checkout 2f4e82f
   ```
   **Expected result:** the repo is cloned, and `git log -1 --format=%h` prints `2f4e82f`.

2. Create and activate a Python virtual environment:
   ```
   py -m venv .venv
   source .venv/Scripts/activate
   ```
   **Expected result:** the shell prompt now starts with `(.venv)`.

3. Install the dependencies the test needs:
   ```
   pip install pytest "passlib[bcrypt]" "bcrypt<5.0.0" "python-jose[cryptography]" "pydantic-settings"
   ```
   **Expected result:** pip finishes with a line like `Successfully installed ... passlib-1.7.4 ... bcrypt-4.3.0 ...`.

4. Run the xfail test with the marker disabled, so pytest actually executes it:
   ```
   pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail
   ```
   **Expected result:** the test fails with the traceback below. Without `--runxfail`, the same test reports `XFAIL` because the `xfail` marker is expected to fail.

## Observed behavior

The test fails with an uncaught `UnknownHashError` traceback from passlib. The exception escapes `verify_password` instead of being caught and turned into a `False` return. Excerpt of the traceback below (the test-file frame and the `raise` line are trimmed for length):

```
core\security.py:37: in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.venv\Lib\site-packages\passlib\context.py:2343: in verify
    record = self._get_or_identify_record(hash, scheme, category)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.venv\Lib\site-packages\passlib\context.py:2031: in _get_or_identify_record
    return self._identify_record(hash, category)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.venv\Lib\site-packages\passlib\context.py:1132: UnknownHashError
E           passlib.exc.UnknownHashError: hash could not be identified
```

Full pytest summary line:

```
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format - passlib.exc.UnknownHashError: hash could not be identified
```

## Expected behavior

`verify_password("password", "not_a_valid_bcrypt_hash")` should return `False`. The malformed hash should be treated as a failed verification, not raised as an exception.

## Eval iterations

**Run history**

Score: 17/20 (full run, first Unit 2 rubric, category floor unmet: no match in disclosure)
Score: 17/20 (full run, after splitting the disclosure check, category floor met)
Score: 19/20 (full run, final, PASS, saved to eval-run.txt)

Additional `--only` checks while revising: 1/3 (pkg-10, pkg-19, pkg-20), then 3/3 after the split check, then 5/5 (pkg-03, pkg-07, pkg-10, pkg-19, pkg-20) as canaries before the confirming full run.

The last score in the list matches the agreement line in the committed `eval-run.txt`:

```
agreement: 19/20 scored items  (bar: 18/20: PASS)
```

**Package analysis**

pkg-19. My rubric accepted it, and the gold label rejected it.

My rubric's `ai_disclosure_if_required` check is written to fire only when the repo-facts block contains an explicit, imperative disclosure requirement. On the final run, the model read pkg-19's repo-facts language as descriptive rather than imperative and passed the package. The gold label treats that same package as the disclosure wall: the repo's stated policy requires disclosing AI assistance and the comments do not disclose, so it should reject. My rubric hedged on the wording. I added "When the requirement is not explicit and imperative, default to PASS" to stop false rejects on pkg-03 and pkg-07, and that hedge is what costs me pkg-19. The check now reads as a compromise between catching the wall and not over-firing on general AI policy language.

**Check rationale**

`ai_disclosure_if_required` — "The repo-facts block of the package, read first, then the claim comment and repro comment | Step 1: read the repo-facts block. Step 2: determine whether it contains an EXPLICIT, IMPERATIVE disclosure requirement — text that uses commands like "must", "required", or "shall" and specifically names AI assistance, e.g. "AI-assisted contributions must be disclosed", "contributors must state when AI was used", or "AI-generated content must be labeled". The following do NOT count as a disclosure requirement and therefore PASS: general statements of AI policy, encouragement of transparency, bans or limits on AI-generated code, descriptions of how the project uses AI, or any language that does not command contributors to disclose AI use. Step 3: if an explicit imperative disclosure requirement exists, then check the claim comment AND the repro comment separately. Each one that carries its own explicit AI disclosure line (for example, "AI-assisted", "prepared with AI assistance", "used AI", or equivalent) passes; if EITHER comment lacks its own disclosure line, FAIL. If no explicit imperative disclosure requirement exists, PASS. When the requirement is not explicit and imperative, default to PASS."

The check went through three forms. The first version asked the model to identify any policy on AI assistance and failed the package if either comment lacked disclosure. It over-fired on pkg-03 and pkg-07, where the repo-facts block stated general AI policy but did not require disclosure. The second version narrowed to "explicit, imperative" but still over-fired on pkg-19 once. This third version splits the requirement into three concrete steps and explicitly lists what does not count, so the model has to identify a command before it fails anything. I kept the final hedge ("when not explicit and imperative, default to PASS") because in the eval set the only disclosure-wall package is one. Without the hedge the check costs more packages than it gains.

**Trade-offs**

This check gives up pkg-19 on the final run. The gold label treats pkg-19 as the disclosure wall. The repo's stated policy requires disclosure and the comments do not disclose, so a stricter version of the check would reject it. But the stricter version I tried first also rejected pkg-03 and pkg-07, which are accept-gold packages whose repo-facts blocks state AI policy without requiring disclosure. The check is a spectrum. Leaning strict catches pkg-19 and loses pkg-03 and pkg-07. Leaning lenient keeps the two accepts and lets pkg-19 through. The final rubric trades one scored miss (pkg-19) for two scored hits (pkg-03, pkg-07), which is the better deal at 19/20. If I had budget for another full run I would try adding a single negative example to the pass condition, for example "a repo-facts block that says 'AI-assisted contributions must be disclosed' is an explicit requirement, while 'we encourage transparency about AI use' is not", to sharpen the boundary. I chose not to spend another approximately $4 chasing one package.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.