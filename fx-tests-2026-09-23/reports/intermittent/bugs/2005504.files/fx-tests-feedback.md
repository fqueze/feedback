## `fx-tests task` reports "0 tests" for a gtest job whose resource-usage profile has per-test markers

- Command: `fx-tests task TBhTIprWToCbnN7zwp1GKw --profiles` (test-windows11-32-25h2/debug-gtest-1proc)
- Expected: the job's gtest outcomes, including `FAIL DesktopLauncherDownloaderReal.DownloadNightlyStub`.
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all", plus a warning listing 14 "failing markers ... named no test path" (FontFallbackTest..., WebGLTextureUploadInit..., "and 9 more"), which truncates away the failure I was looking for.
- The resource-usage profile does have `test` markers per gtest with `PASS`/`FAIL — <Suite.Case>` and durations (`profiler-cli thread markers --search DesktopLauncher --list` shows them).
- Workaround: loaded the resource-usage profile in profiler-cli, and grepped the live log.

## Question: "how often does this gtest fail, per config, out of how many runs — and how long does it take when it passes?"

- Command: `fx-tests test DesktopLauncherDownloaderReal.DownloadNightlyStub` (and the .cpp path) -> "No test path in the xpcshell and mochitest 21-day data".
- `fx-tests intermittent --bug 2005504` knows the bug and its jobs, but nothing gives the denominator or passing-run durations for a gtest.
- Workaround: Treeherder jobs API per job type for the run count, and downloaded 20 passing live logs (~50 MB each) to grep the per-test `took Nms`. Per-test durations for passing runs were the key evidence here (the failure is a 5 s timeout, passes range 0.1–4.5 s).
