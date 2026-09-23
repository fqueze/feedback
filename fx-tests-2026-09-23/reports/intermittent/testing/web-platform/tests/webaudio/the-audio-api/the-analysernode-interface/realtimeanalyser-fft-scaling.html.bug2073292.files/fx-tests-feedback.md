# fx-tests feedback (realtimeanalyser-fft-scaling.html.bug2073292)

## `fx-tests test` has no wpt data

- Command: `fx-tests test testing/web-platform/tests/webaudio/the-audio-api/the-analysernode-interface/realtimeanalyser-fft-scaling.html` (and `--history`)
- Expected: pass/fail counts per config for a wpt test.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". `--harness` only takes xpcshell|mochitest.
- Workaround: `fx-tests intermittent --bug`, then grepping each job's `live_backing.log`. That gives annotated jobs, but no run count and no rate.

## `fx-tests task` leaves out the failure the job was starred for, and lists expected results as failing

- Command: `fx-tests task Eag6OU4VQWupt9fwnVdN9w --profiles`
- Expected: the job's one unexpected result, `TEST-UNEXPECTED-PASS | .../realtimeanalyser-fft-scaling.html | FFT scaling tests - ... - expected FAIL`.
- Got: "FAILED (19)", made of ERROR/TIMEOUT tests such as `/infrastructure/expected-fail/*` and `/ai/language_detection/*`. Their metadata expects those results, and the log has no TEST-UNEXPECTED line for any of them. The UNEXPECTED-PASS subtest is not listed, because its test is `OK`.
- Workaround: `rg TEST-UNEXPECTED` on the job log. For a wpt job, "failing" should mean unexpected, and should include subtest results.
