## `fx-tests task` shows a different message for the same failure in different jobs

- Command: `fx-tests task <taskId> --profiles --limit 0` on the 6 msix jobs of test_webext_apis.js
- Expected: the same message for the same failure mode, since every job's profile holds both the `test` marker message `Test timed out` and the ERROR `... | Timed out and was force-killed by the harness; ...`.
- Got: `Test timed out` in JOcVul, FADMjP, ZtblAr, PyUxuO, and the force-killed ERROR in Q6pDc4 and LKys8j, which reads like two failure modes.
- Workaround: loaded the profiles and checked that both messages are there in each.
