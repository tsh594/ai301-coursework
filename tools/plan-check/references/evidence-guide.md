# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

**Where it lives:** The plan's diagnosis section states the cause; the repro-evidence block's traceback and observed-behavior sections are the ground truth the cause must be consistent with. In live mode: the draft plan's diagnosis section, read against the student's posted repro comment on the issue.

**What good looks like:** The stated cause cites behavior the repro evidence actually shows (for example, "the call to `pwd_context.verify` at `core/security.py:37` raises `UnknownHashError` because passlib cannot identify the stored value"). A cause that contradicts the traceback, or that names an error the evidence does not show, fails.

## Scope

**Where it lives:** The plan's scope statement, naming what is in scope, what is out of scope, and the files or areas to be touched.

**What good looks like:** The plan names a bounded set of files or functions (one to three is typical for a first contribution), states at least one thing it will not change (for example, "I will not change `hash_password` or the JWT code"), and does not describe an open-ended rewrite.

## Executability

**Where it lives:** The plan's approach and order-of-work sections.

**What good looks like:** A stranger knows which file to open first, what change to make, and in what order. Each step names a specific action on a specific file or function, not an abstract intent like "improve error handling".

## Test plan

**Where it lives:** The plan's test plan section, read against the repro-evidence steps and artifacts.

**What good looks like:** The plan names specific observable checks (a named test, a named command, a named output), re-runs the repro evidence's steps, and states what should change (for example, "the test `test_verify_with_wrong_hash_format` should pass after removing the xfail marker"). Vague promises like "tests pass" fail.

## Honesty

**Where it lives:** The plan's risks and unknowns section, or its absence, read against the certainty of the plan's other claims.

**What good looks like:** Uncertain things are stated as uncertain. An honest "I have not tested whether `ValueError` also needs to be caught; I will check during the build" passes. A confident "this fix resolves every case" without evidence fails.

## Comms

**Where it lives:** The plan comment, read against the thread highlights (maintainer signals, other students' plans) and the repo-facts block (templates, contributing asks, AI-disclosure policy).

**What good looks like:** The comment is this issue's plan, in the student's own words, not a generic template. It honors any maintainer signal in the thread (for example, "if PR #78 already takes a broader approach, I'll defer to it"). If the repo-facts block carries an explicit imperative AI-disclosure requirement, the comment discloses.