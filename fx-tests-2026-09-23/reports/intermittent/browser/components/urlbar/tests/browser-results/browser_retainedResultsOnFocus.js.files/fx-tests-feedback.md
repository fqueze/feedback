# fx-tests feedback — browser_retainedResultsOnFocus.js

## `fx-tests test` says "passing" for a test whose only failures are test-verify jobs

- Command: `fx-tests test browser/components/urlbar/tests/browser-results/browser_retainedResultsOnFocus.js` (and `--history`)
- Expected: the 9 test-verify failures of 2026-09-07 (inside the 2026-09-01 … 09-21 window), or a line saying test-verify jobs are not counted.
- Got: `15,870 runs 15,870 pass (100.00%) 0 fail`, `Verdict: passing`, and every day of `--history` at 0 fail. Nothing says TV jobs are excluded, so the bug looks like it has never failed in the window.
- Workaround: `fx-tests intermittent --bug 2069762 --since 30 --tree all` found the 9 TV jobs; then `fx-tests task <id>`.

## `fx-tests task` says "Passed when the harness reran it" when the retry failed too

- Command: `fx-tests task eUi2TQ2GRJKrQ7EO21RDrg --profiles` (same on `Xml2zQeeS7OxAtE5dxPZ_g`)
- Expected: "the retry failed too" — four profiles (`…js.json`, `-2`, `-3`, `-4`) are listed, which per the brief means retries failed.
- Got: `FAIL — 4 failing executions of 12` / `Passed when the harness reran it.`
- The resource-usage profile shows the opposite: test-verify step 1 (`--repeat 10 --run-until-failure`) ran PASS, PASS, FAIL in the nova manifest, then in its `retry` (marker at 21m7s) PASS, PASS, FAIL again (FAIL at 21m37s); same for the proton manifest and its retry. Probably the passing repeats inside the retry are read as "the retry passed". Under repeat/run-until-failure, "passed on retry" should mean no failing execution in the retry.
