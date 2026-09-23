## Question: which tests are behind one crash signature?

- Command: `fx-tests crashes --harness xpcshell --signature "child process hang" --path browser/components/urlbar --limit 0`
- Expected: the list of tests that crashed with that signature (with counts), as `failures --tests` does for messages.
- Got: one row (`1,786 crashes, 28 tests`) and no way to list the 28 tests in text output; `crashes` has no `--tests` flag.
- Workaround: `--json` and read `rows[].tests[]`. A `--tests` flag on `crashes` (mirroring `failures --tests`) would have answered it.

## `failures --message` does not find crash signatures

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests behind that message, since `fx-tests test` lists it under "Issues" as the first failure of 86 runs.
- Got: "No failure matched." It is only reachable through `fx-tests crashes --signature`. Saying so in the no-match message ("this string is a crash signature; try `fx-tests crashes --signature`") would have saved a round trip.

## Verdict line contradicts its own numbers when most failures have no mode

- Command: `fx-tests test browser/components/urlbar/tests/unit/test_frecency_alternative_nimbus.js`
- Got: "Fails almost only in parallel: 4 of 5 mode-recorded failures, 0.0% of parallel runs against 1.1% of sequential ones". The rates say the opposite of the headline, and 90 of the 95 failures (all the CRASHes) have no mode recorded (`--executions`), so 5 failures decide the headline.
- Expected: either no parallel/sequential verdict when 95% of failures are unrecorded, or a line saying so. `fx-tests task <id>` did know these crashes were parallel-only ("All 44 failed only in the parallel phase").
