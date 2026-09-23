# fx-tests feedback (2035284, gtest DesktopLauncherDownloaderReal.DownloadBetaStub)

## Question: how often does a gtest pass and fail, per config, and how long do its passing runs take?
- Command: `fx-tests test DesktopLauncherDownloaderReal.DownloadBetaStub` and `fx-tests test browser/app/desktop-launcher/tests/gtest/DesktopLauncherDownloaderTest.cpp`
- Expected: per-config pass/fail rates and durations, as for xpcshell/mochitest.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". gtest is not covered at all.
- Workaround: a script over Treeherder `/api/jobs/?push_id=` for 60 autoland pushes, then each Windows gtest job's `profile_resource-usage.json`, reading the `Test` markers (status + duration). 134 profiles, about 10 minutes. The resource-usage profiles already hold every gtest's status and duration, so the data is there for `fx-tests test` to cover gtest.

## Question: what failed in this gtest job?
- Command: `fx-tests task Vi42DZCaTYW_pc0GpCIvzA --profiles`
- Expected: the failing gtests (DesktopLauncherDownloaderReal.DownloadBetaStub, DownloadDevStub).
- Got: "0 tests, 0 executions, 0 failing" and "This profile records no tests at all", although the resource-usage profile has a `test` marker per gtest with its status. The warning lists "15 failing markers ... FontFallbackTest.ListFontsUsedForString_FallbackOrder, ... WebGLTextureUploadInit..." first: those are GTEST_SKIP'd tests logged as `TEST-PASS | <reason>` then `TEST-FAIL | took 1ms` (expected, not UNEXPECTED), so the only real failures are buried in "and 10 more".
- Workaround: read the job log and `profiler-cli thread markers --search DesktopLauncher`.

## Question: since when has a bug been annotated (beyond 7 days)?
- Command: `fx-tests intermittent --bug 2035284`
- Got: only 2026-09-16 to 2026-09-22.
- Workaround: Treeherder `/api/failuresbybug/?startday=2026-03-01&endday=2026-09-23&tree=all&bug=2035284` (data back to 2026-06-02).
