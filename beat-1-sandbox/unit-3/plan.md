# Plan for #72: verify_password fails closed on malformed stored hashes

## Diagnosis

`verify_password` in `core/security.py` passes the stored hash directly to `pwd_context.verify()` with no error handling. When the stored value is not a format passlib recognizes, `passlib.exc.UnknownHashError` escapes to the caller instead of the function returning `False`.

My repro comment on the issue pinned this at `core/security.py:37`:

```
core\security.py:37: in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.venv\Lib\site-packages\passlib\context.py:2343: in verify
    record = self._get_or_identify_record(hash, scheme, category)
.venv\Lib\site-packages\passlib\context.py:2031: in _get_or_identify_record
    return self._identify_record(hash, category)
.venv\Lib\site-packages\passlib\context.py:1132: UnknownHashError
E           passlib.exc.UnknownHashError: hash could not be identified
```

The malformed hash is `"not_a_valid_bcrypt_hash"`. The issue also names `tests/unit/test_security.py::test_verify_with_wrong_hash_format`, which carries a `@pytest.mark.xfail(strict=True)` marker referencing manifest id H-05. That marker is doing the failing on purpose, so the bug is hidden until the marker is bypassed with `--runxfail`.

At least five classmates on the thread reported a second shape: a hash with a valid-looking `$2b$` prefix but corrupt internals (truncated salt, out-of-range cost) raises a plain `ValueError`, not `UnknownHashError`. I have confirmed in my own environment (passlib 1.7.4) that `UnknownHashError` subclasses `ValueError`, so catching `ValueError` covers both shapes.

## Scope

**In scope:**
- `core/security.py`: wrap the `pwd_context.verify` call inside `verify_password` with a `try/except ValueError` and return `False` on error.
- `tests/unit/test_security.py`: remove the `@pytest.mark.xfail(strict=True)` marker on `test_verify_with_wrong_hash_format`. The test then runs as a normal regression test.

**Out of scope:**
- `hash_password`, `create_access_token`, `decode_access_token` — untouched.
- `api/routes/auth.py` — the login route already treats a `False` return as a failed login and does not need a change.
- `CryptContext` configuration, dependencies, other xfail markers, and unrelated lint or typecheck findings.
- Logging the exception. The issue asks for a fail-closed return, not a log line.

## Files to change

1. `core/security.py`
2. `tests/unit/test_security.py`

Two files. No others.

## Branch

`fix/72-verify-password-fail-closed`

## Approach

1. Read `core/security.py` lines 27-37 to confirm the current shape of `verify_password`.
2. Add a `try/except ValueError` around the `pwd_context.verify(...)` call. On exception, return `False`. Keep the success path inside the `try` so a successful verify still returns its boolean.
3. Update the docstring's "Returns" section to mention that a malformed stored hash and an oversized password both return `False`.
4. Open `tests/unit/test_security.py` and delete the `@pytest.mark.xfail(strict=True, reason=...)` decorator on `test_verify_with_wrong_hash_format`. Do not change the test body.
5. Run the test suite (see Test plan).

## Test plan

Before the fix (from my Unit 2 repro):

```
$ pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v
# 1 xfailed

$ pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail
# 1 failed with passlib.exc.UnknownHashError
```

After the fix:

1. Re-run my repro step 4 without `--runxfail`. Expected: `1 passed`.
2. Add these new checks and run them (my repro did not include them):
   - Direct call with `"not_a_valid_bcrypt_hash"`. Expected: returns `False`, no traceback.
   - Direct call with a truncated `$2b$12$abc`. Expected: returns `False`, no traceback. This is the case classmates reported; the check confirms whether my `except ValueError` catches it.
   - Direct call with an oversized password: `verify_password("p" * 5000, hash_password("p"))`. Expected after the fix: returns `False` (before the fix, this raised `PasswordSizeError`). This is a new behavioral change the fix introduces and I want it tested, not assumed.
   - Valid-hash controls: `verify_password("password", hash_password("password"))` → `True`; wrong password → `False`. Confirms the success path is unchanged.
3. Run `pytest tests/unit/test_security.py -v` in full. Expected: all tests pass, none xfailed. `test_hash_password_long_input` (defined at lines 209-216) already exercises a 1000-character password against `verify_password` and expects `True`; this run confirms my catch does not break it. Note that test uses 1000 characters, which is under passlib's 4096-character limit, so it does not exercise the `PasswordSizeError` path.

If any of these steps fail, that is a deviation and goes under `## Deviations`.

## Risks and unknowns

- **Catch width.** Catching `ValueError` is broader than catching only `UnknownHashError`. It also turns two other passlib raises into `False` returns:
  - An oversized password (over 4096 characters) currently raises `PasswordSizeError`. After the fix, `verify_password` returns `False` for it. I have verified in passlib 1.7.4 that `PasswordSizeError` subclasses `ValueError` (via `PasswordValueError`), so my catch will trigger. This is fail-closed behavior consistent with the issue's request, and I am accepting it: an oversized password cannot match a real credential, so returning `False` is safe. The alternative is to re-raise `PasswordValueError` before the catch, which a classmate proposed; I am not doing that because the issue asks for a fail-closed return and the extra branch adds complexity for a case the login route already handles as a failed login.
  - Any other `ValueError` raised by an internal passlib call. I have not enumerated these. I will test the cases above and note anything that surfaces.
- **No logging.** A corrupt stored hash will look identical to a wrong password from the caller's perspective. This is what the issue asks for, but it is a real trade-off and I am noting it here rather than silently accepting it.

## Deviations

Nothing in the plan changed during the build. Both files were edited exactly as described: `core/security.py` now wraps `pwd_context.verify` in `try/except ValueError` and returns `False`, and `tests/unit/test_security.py` no longer carries the `@pytest.mark.xfail` decorator on `test_verify_with_wrong_hash_format`.

Results matched the plan's expectations:

- The covering test goes from `1 failed` with `--runxfail` (before the change) to `1 passed` (after).
- Direct calls: `"not_a_valid_bcrypt_hash"` returns `False`, `"$2b$12$abc"` returns `False`, oversized password (`"p" * 5000`) returns `False`, correct password against its own hash returns `True`, wrong password returns `False`.
- Full test file: `25 passed, 1 warning`, no xfailed tests.

One observation not in the plan: passlib 1.7.4 prints a `(trapped) error reading bcrypt version` traceback to stderr the first time it loads bcrypt 4.x. This is a known compatibility warning between passlib 1.7.4 and bcrypt 4.x; it does not affect behavior and was already present before this change. I left it alone because it is out of scope for this issue.