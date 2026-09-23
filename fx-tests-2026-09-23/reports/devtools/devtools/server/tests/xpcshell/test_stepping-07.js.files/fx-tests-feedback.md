## One failure sequence shown as two different messages across jobs

- Command: `COLUMNS=300 fx-tests task <taskId> --profiles --limit 0 --messages` for the six msix jobs of `devtools/server/tests/xpcshell/test_stepping-07.js`
- Expected: the same message for the same sequence. All six have the same markers: a `TIMEOUT` test marker with message `Test timed out`, and a replayed `ERROR ... | Timed out and was force-killed by the harness; ...`.
- Got: four jobs show `1x ... Timed out and was force-killed by the harness; ...`, two (ZtblArCLSx6O6KzfEkL1YQ, LKys8j8iTQKTAN9hKv040g) show `Test timed out`. It looked like two failure modes until the profiles were read.
- Workaround: loaded the profiles to compare.

## Question: did this test run at all, or did the job break before it started?

- Command: `fx-tests test devtools/server/tests/xpcshell/test_stepping-07.js` gives `Verdict: intermittent` and `6x TIMEOUT Test exceeded time limit`; `fx-tests task <id>` gives `1060 TIMEOUT` of 1472 in the job.
- What would have answered it: a per-occurrence flag when the job had hundreds of TIMEOUTs with the same ~45 s duration, or when the harness logged `will retry` within ~40 ms of the test's start with no test output. I counted TIMEOUTs by start time and duration with a script over `profiler-cli ... --json`.
