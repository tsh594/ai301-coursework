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