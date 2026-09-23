## Why a failing test has no per-test profile

- Command: `fx-tests task HbTRtfuuTHCy31gimlSikg --profiles` (and the 11 other failing tasks, plus the try push's b9m3T5-qSPG3d0ItqVggcw and Qlph936QQKSivmiDHEKbWg).
- Question: where is the per-test profile of browser_theme_switching.js, or why is there none?
- Expected: a reason next to "No failing test named a per-test profile in this job".
- Got: only that sentence. The reason was in the job log: an earlier test of the same manifest, browser_telemetry_toolboxtabs_jsprofiler.js, prints "This test starts and stops the profiler and is not compatible with the use of MOZ_PROFILER_STARTUP. Stopping the profiler before starting the test." Every later test in devtools/client/shared/test/browser.toml runs without the profiler, so none of their first-run failures can have a profile. The same console.log is in the resource-usage profile, so fx-tests could spot it.
- Workaround: downloaded live_backing.log and grepped it.
