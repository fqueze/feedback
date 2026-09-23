## gtest not covered by `fx-tests test` (bug 2033214 report)

- Command: `fx-tests test DesktopLauncherDownloaderReal.DownloadDevStub` (also tried the .cpp path).
- Expected: per-config pass/fail rates and durations for a gtest.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...".
- Question it could not answer: "how often does this gtest fail per run, and what do its passing durations look like?"
- Workaround: Treeherder `api/failuresbybug` for counts, then listing Windows gtest jobs on the last 40 autoland pushes via the Treeherder jobs API and grepping 77 live_backing.log files for `took Nms`. ~75 curl calls.

## `fx-tests task` says a gtest job "records no tests at all"

- Command: `fx-tests task OOsk67T3Rbu7BNmSKTDS2w --profiles` (test-windows11-64-25h2-asan/opt-gtest-1proc).
- Expected: the job's gtest results, since its resource-usage profile has a `test` marker per gtest with status (e.g. `FAIL — DesktopLauncherDownloaderReal.DownloadDevStub`).
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one", right after a warning listing 14 FAIL markers it could not attribute to a test path.
- Workaround: loaded the resource-usage profile in profiler-cli and searched the `test` markers directly.
