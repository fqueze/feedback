## `task --profiles` / `--messages` present TEST-KNOWN-FAIL todos as the test's failure

- Command: `fx-tests task KhBS8BX_SbuvfyprrYFMqw --profiles` (and `--messages`)
- Expected: the headline under a TIMEOUT row to be the failure (`Test timed out`), and todo (TEST-KNOWN-FAIL) messages either omitted or labelled as known failures.
- Got: the one-line headline was `handleEvent() was unable to perform a11y checks on hidden node: id: main-button ...`, and `--messages` listed `Clicked button#main-button inside panel (state="closed"), which was not open...` and that a11y message before `Test timed out`, all as "1x" failure messages. In the profile both are `TEST-KNOWN-FAIL` markers from earlier subtests; the only `TEST-UNEXPECTED-FAIL` is the timeout. Same in all six tasks.
- Cost: I first read the ClickChecks message as the failure and fetched ClickChecks.js before the profile showed it was a todo.
- Workaround: read the Test-category markers in the profile to see which lines are UNEXPECTED.

