## `fx-tests task` on a geckoview-junit job counts expected failures as failing, and lists no tests

- Command: `fx-tests task TMQTFgp9Q5mxsVFbQA6Ndg --profiles` (also `LYIHbUnRRDK0RF_27bj9pA`)
- Expected: the junit tests of the job with their status, and the one real failure
  (`TEST-UNEXPECTED-TIMEOUT | runjunit.py`).
- Got: `0 tests, 0 executions, 0 failing`, "This profile records no tests at all", and a warning
  that "113 failing markers in this job named no test path", e.g.
  `FAIL org.mozilla.geckoview.test.AutofillDelegateTest#autofillIframe[#oop]`. Those are junit
  assumption failures ("Todo"), logged by runjunit as `TEST-FAIL` with an expected status: their
  `test` markers carry `color: green` (e.g. `TextInputDelegateTest#updateSelectionWithoutTextChange`).
  The resource-usage profile does have 1221 `test` markers, so "records no tests" is wrong too.
- Workaround: read the log and the profile's `test` markers directly.

## Question: which of a bug's annotated jobs actually show the bug's failure?

- Command: `fx-tests intermittent --bug 1554779 --limit 0`
- The "Failure messages" section put 49 jobs under "Some tests did not run (probably due to a
  crash in the harness)", but that line follows both a real `Timed out after 3000 seconds` (46
  jobs) and an instrumentation-process crash in `ProfileLockedTest#profileLocked` after ~750
  tests (2 jobs). Telling them apart took streaming all 53 logs and grepping them.
- What would have answered it: the first `TEST-UNEXPECTED-*` line of each occurrence in the
  Occurrences table (or a grouping on it), plus the suite duration.
