# Evidence guide: where proof lives in a reproduction package

## Environment

**Where it lives:** The repro report's environment section, and the
repo's own setup docs (README.md, CONTRIBUTING.md, or equivalent).

**What good looks like:** The report names the operating system, the
language/runtime version (e.g., Python 3.11.4), the package manager
version (e.g., pip 23.x), and the commit or version of the project
being tested. Where the issue names a target environment, the report
either matches it or explicitly calls out the difference.

## Steps

**Where it lives:** The reproduction steps section of the repro report,
starting from a clean checkout or a clearly stated starting state.

**What good looks like:** The steps are numbered or sequential, start
from a state a stranger can reach (clean clone, standard setup), and
end at the observed behavior. Following them requires no questions and
no unstated commands. Any step that depends on an environment variable
or a specific file names it inside the step.

## Behavior shown

**Where it lives:** The output excerpt, log, or screenshot in the repro
report, read against the error the issue describes.

**What good looks like:** The artifact shows the same class of behavior
the issue reports — the same exception type, the same wrong output, or
the same failing assertion. If the issue describes a Python exception,
the excerpt includes the exception and its traceback. The report does
not present an adjacent behavior (a different error, a different code
path) as if it were the issue's.

## Honesty

**Where it lives:** The stated outcome in the report, read against the
report's own artifacts.

**What good looks like:** The stated outcome matches what the evidence
shows. An honest cannot-reproduce — "I followed the steps and did not
see the issue's behavior, here is my output" — is a valid outcome and a
pass. A confident claim that goes beyond what the artifact shows (a fix
with no diff, a cause with no evidence) is not.

## Comms

**Where it lives:** The claim comment and the repro comment, read
against the issue thread and the repo's contribution policy.

**What good looks like:** The claim names the specific function, file,
and error from the issue, and promises an investigation and a report,
not a fix or a date. The repro comment presents the contributor's own
work, not a piggyback on a classmate's. Where the repo's policy
requires disclosing AI assistance, the comment discloses it explicitly.
Where the repo has a template for comments or PRs, the comment respects
its substantive content.