## `fx-tests task` says "Passed when the harness reran it" when the rerun failed

- Command: `fx-tests task KRhYU0waTpKjxgGTOhCBrw --profiles` (also `eYxQI4bJTWi9ls3EiHvtyg`), a
  test-verify job.
- Expected: the retry to be reported as failing: the `-2` profile it lists shows the retry, in a
  fresh browser, failing the same way (iteration 1 passes, iteration 2 fails in setup).
- Got: `FAIL — 2 failing executions of 4` / `Passed when the harness reran it.` In verify mode
  every run repeats the test, so a PASS execution after the FAIL one does not mean the rerun
  passed.
- Workaround: loaded the `-2` profile to find out.
