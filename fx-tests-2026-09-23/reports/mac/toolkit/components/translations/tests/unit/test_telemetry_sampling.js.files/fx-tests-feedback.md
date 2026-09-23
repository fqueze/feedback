# fx-tests feedback (test_telemetry_sampling.js)

## The per-config rate of a parallel-only failure hides a near-perma-fail
- Command: `fx-tests test toolkit/components/translations/tests/unit/test_telemetry_sampling.js`
- Expected: with the verdict "Fails almost only in parallel", the per-config table to show the parallel-phase rate too.
- Got: `test-macosx1500-aarch64/debug-xpcshell 49.3%`. Its real rate in the parallel phase is 97.2% (243/250): every failure is retried sequentially and passes, so the retries double the denominator. Same for windows10 debug (43.3% shown, 76.3% parallel) and linux2404-64-artifact/debug (~40% shown, 70.1% parallel).
- Workaround: one `fx-tests test <path> --config <c> --executions` call per config.

## `--durations` mixes the harness retries with the parallel runs
- Command: `fx-tests test <path> --durations`
- Expected: durations split by execution mode, or at least flagged when most passing runs are sequential retries.
- Got: `test-macosx1500-aarch64/debug-xpcshell 250 runs, median 15687 ms`: 243 of those 250 passes are sequential retries, so the median says "15 s" for a test that almost never finishes within 120 s in the parallel phase. Read naively it says the test is fast there.
- Workaround: cross-check with `--config <c> --executions`.

## `--bugs` prints nothing when no bug names the test
- Command: `fx-tests test <path> --bugs`
- Expected: a "No bug names this test" line.
- Got: the normal output with no bugs section at all, indistinguishable from the flag being ignored. `--json` shows `annotatedBugs: []`.
- Workaround: `--json`.

## `fx-tests task <id> --profiles` truncates the FAILED list with no visible cue in a grep
- Command: `fx-tests task MKiOFl-6RcaqTI4Wr9y5ow --profiles | grep -A5 test_telemetry_sampling`
- Expected: the test (it failed in that job).
- Got: nothing; it only appears with `--limit 0` (36 failures in that job).
- Workaround: `--limit 0`. Possibly worth a `--test <path>` filter on `task`, since "this test's profile in this job" is the usual question.
