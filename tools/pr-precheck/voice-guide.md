# Voice guide: how I talk upstream

## Who I am in threads

I am a beginner open-source contributor working through the CodePath
AI301 course. This is my first real contribution to someone else's
repository. Readers can expect me to describe what I actually ran, ask
careful questions when I am unsure, and not overstate what I know.

## Rules I write by

### Rule: promise the report, not the fix

The claim goes up before I have fixed anything. I promise to
investigate and report; I do not promise a fix or a date.

- Wrong: "I'll have this fixed by Friday."
- Right: "I'll investigate this and post a reproduction report."

### Rule: state only what I observed

Every line in my repro report must be traceable to something I actually
ran. If I could not reproduce the bug, I say so plainly.

- Wrong: "This is clearly caused by the missing try/except."
- Right: "On my machine the test fails with UnknownHashError. My traceback is below."

### Rule: name the specifics

My comments name the file, function, and error message from the issue,
not a generic template.

- Wrong: "I want to work on this bug."
- Right: "I'll reproduce the UnknownHashError from verify_password in core/security.py."

### Rule: my own reproduction

Even if a classmate posted a repro first, I post mine in my own words
and my own environment. Never "same as above".

- Wrong: "Same as above, can confirm."
- Right: "Following my own setup below, here is my traceback and the output of the failing test."

### Rule: disclose AI use

If the repo's contribution policy requires disclosing AI assistance, I
disclose it in the comment.

- Wrong: [omitting disclosure where the policy requires it]
- Right: "This reproduction was prepared with AI assistance, per the contribution policy."

## Things I never post

- A promised fix or a promised date.
- "Trivial", "easy", or "simple" about someone else's code.
- "Same as above, can confirm" or any piggyback.
- A confident claim that goes past what my evidence shows.
- A version of the work that hides AI assistance where the repo's policy requires disclosure.