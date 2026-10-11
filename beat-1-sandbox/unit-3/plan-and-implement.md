# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of the plan you posted on your issue, the branch you built on, and
the evaluation runs that produced `eval-run.txt`. This file is graded at
the path above; a copy kept anywhere else in the repository is not read.

---

## Your identity upstream

**GitHub username**

tsh594

---

## Posted upstream

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-6112840743

Plan for #72, built from my reproduction above (commit 2f4e82f; Windows 11, Python 3.14.7, passlib 1.7.4, bcrypt 4.3.0, pytest 9.1.1).

I have seen the open PRs on this issue (#78, #91, #92), all from classmates. Per the course house rules I am building my own from my own reproduction.

**Diagnosis.** `verify_password` in `core/security.py:37` calls `pwd_context.verify(...)` with no error handling. When the stored hash is not a recognizable format, passlib's `UnknownHashError` escapes instead of the function returning `False`. My repro comment shows the traceback ending in `passlib.exc.UnknownHashError: hash could not be identified`. Several classmates on this thread reported that a hash with a valid-looking `$2b$` prefix but corrupt internals raises a plain `ValueError` instead, and I have confirmed in my own environment that `UnknownHashError` subclasses `ValueError`, so a single catch covers both shapes.

**Change.** In `core/security.py`, wrap the verify call in a `try/except ValueError` and return `False` on error. In `tests/unit/test_security.py`, remove the `@pytest.mark.xfail(strict=True)` marker on `test_verify_with_wrong_hash_format` so it runs as a normal regression test. Two files, on branch `fix/72-verify-password-fail-closed`.

**Not changing.** `hash_password`, the JWT functions, `api/routes/auth.py` (it already treats `False` as a failed login), the `CryptContext` config, other xfail markers, and unrelated lint or typecheck findings.

**Test plan.** Re-run my repro step 4 (the covering test with `--runxfail`): it goes from `1 failed` to `1 passed`, and without `--runxfail` from `1 xfailed` to `1 passed`. Add these new checks my repro did not include: direct calls with `"not_a_valid_bcrypt_hash"` and a truncated `$2b$12$abc` hash (both should return `False`), an oversized password (`"p" * 5000`) which currently raises `PasswordSizeError` and should return `False` after the fix, and valid-hash controls (`True` for the right password, `False` for the wrong one). Run `pytest tests/unit/test_security.py` in full.

**Risks and unknowns.** Catching `ValueError` is broader than catching only `UnknownHashError`. I have verified in passlib 1.7.4 that `PasswordSizeError` subclasses `ValueError`, so the fix also turns an oversized password from a raised error into a `False` return. I am accepting that as fail-closed behavior and testing it explicitly. I have not enumerated every other `ValueError` passlib can raise from inside `verify`, so I will check the cases above and note anything that surfaces. The fix also makes a corrupt stored hash indistinguishable from a wrong password to the caller, which is what the issue asks for, but I am naming it as a trade-off rather than leaving it silent.

I used Claude Code to help draft this comment. I ran the reproduction myself and will run the build and tests myself.

**Branch**

fix/72-verify-password-fail-closed

---

## Evidence

**Before the fix**

```
$ pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail
```

Result:

```
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format FAILED   [100%]

=================================== FAILURES ===================================
_______________ TestSecurity.test_verify_with_wrong_hash_format _______________
tests\unit\test_security.py:227: in test_verify_with_wrong_hash_format
    result = verify_password("password", wrong_hash)
core\security.py:37: in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
.venv\Lib\site-packages\passlib\context.py:2343: in verify
    record = self._get_or_identify_record(hash, scheme, category)
.venv\Lib\site-packages\passlib\context.py:2031: in _get_or_identify_record
    return self._identify_record(hash, category)
.venv\Lib\site-packages\passlib\context.py:1132: UnknownHashError
E           passlib.exc.UnknownHashError: hash could not be identified
======================== 1 failed, 1 warning in 0.66s =========================
```

**After the fix**

```
$ pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v
```

Result:

```
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED   [100%]
===================================== 1 passed, 1 warning in 0.48s =====================================
```

Direct-call checks (five inputs, run after the fix):

```
$ python -c "
from core.security import hash_password, verify_password
print('malformed hash:', verify_password('password', 'not_a_valid_bcrypt_hash'))
print('truncated hash:', verify_password('password', '$2b$12$abc'))
print('oversized password:', verify_password('p' * 5000, hash_password('p')))
print('right password:', verify_password('password', hash_password('password')))
print('wrong password:', verify_password('nope', hash_password('password')))
"
malformed hash: False
truncated hash: False
oversized password: False
right password: True
wrong password: False
```

Full test file:

```
$ pytest tests/unit/test_security.py -v
...
========================================= 25 passed, 1 warning in 11.32s =========================================
```

No xfailed tests remain. The one warning is a Pydantic V2 deprecation notice in `core/config.py`, unrelated to this change.

## Eval iterations

**Run history**

Score: 3/3 (smoke run, --limit 3, not saved)
Score: 19/20 (full run, saved to eval-run.txt, bar PASS)

The last score matches the agreement line in the committed `eval-run.txt`:

```
agreement: 19/20 scored items  (bar: 18/20: PASS)
```

**Package analysis**

pkg-14. My rubric rejected it; the gold label accepted it.

My rubric's checks `diagnosis_grounded`, `executable_by_stranger`, and `unknowns_stated` all fired against pkg-14. Reading the package again, the diagnosis and the approach are present and followable, and the unknowns are arguably stated, but the plan's wording made each of the three checks read as uncertain rather than passing. The gold label treats the package as a clean accept: the plan is grounded, executable, and honest enough to pass. The three failures are therefore false negatives on my rubric, not real defects in the package. The rubric's pass conditions for these three checks are written tightly enough that a slightly understated plan can trip all three at once, which is the pattern that cost me this package.

**Check rationale**

`unknowns_stated` — "The plan's risks and unknowns section, or its absence, read against the certainty of the plan's other claims | Uncertain things are stated as uncertain. A plan that presents an untested guess as certain fails. A plan that names no unknowns passes only if it also makes no claim that depends on one. | required"

This check went through two forms. The first version simply asked whether a risks section exists, which let pkg-14 fail only if the section was missing entirely. The second version reads the risks against the plan's other claims, so a plan that names risks can still fail if it makes a claim elsewhere that the risks do not cover. That sharpened the check enough to catch the case it was designed for. In the final run it also fired on pkg-14's absent or understated unknowns, which is the reason pkg-14 disagreed with the gold label.

**Trade-offs**

This check gives up pkg-14 on the final run. The gold label accepts the package and my rubric holds it, so the check is stricter than the eval set's own line. The reason I kept it that strict is that `unknowns_stated` was originally written to catch a package that presents an untested guess as certain, which is a failure mode that shows up in real plans and is worse to miss than to flag. On the final run the check is doing that job for the wrong package once out of twenty. I considered loosening it to "the risks section names at least one uncertain item" but that version passed the earlier run's disagreement on pkg-19 without fixing the underlying ambiguity, so I left it as is. Loosening further would need a canary re-run against a plan that presents certainty where it should not, which I did not have budget for in this run.