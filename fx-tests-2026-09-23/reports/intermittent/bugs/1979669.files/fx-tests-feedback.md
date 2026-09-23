# fx-tests feedback (bug 1979669)

## Question: how often does this GPU-process crash happen on Windows, and which test causes it?

- `fx-tests test browser/base/content/test/browser-webrtc/browser_devices_get_user_media_camera_preview.js --coverage`
  - Expected: some crashes on the Windows debug configs, since 14 Windows jobs in 7 days crashed the GPU process during this test.
  - Got: every Windows config shows `ok`, with 0 failures. The test's own status is PASS because the GPU process restarts and the test goes on.
- `fx-tests test browser/base/content/test/browser-webrtc/browser_webrtc_hooks.js --day 2026-09-21`
  - Expected: the crashes the harness pinned on this test (the last one in the manifest), because `fx-tests task QvVZio7uQ6a3AosfRV8N7g` lists `browser_webrtc_hooks.js CRASH — 2 failing executions of 3 @ RustMozCrash`.
  - Got: `336 runs 336 pass`, 0 crashes.
- So `task` and `test` disagree on the same job, and the Windows rate cannot be had from `test` at all. Workaround: counted the annotated Windows jobs by hand, and grepped their logs for `Hit MOZ_CRASH` to find the test that was running.
- It could show: a GPU-process `PROCESS-CRASH` reported at the end of a manifest, counted against the test that was running when the `Hit MOZ_CRASH` line was printed, or at least counted against the test the harness names.

## Question: which crash, and in which test, is behind each job annotated on a crash bug?

- `fx-tests intermittent --bug 1979669 --limit 0`
  - Expected: for each occurrence, the crash signature and the test that was running.
  - Got: "Failure messages, per annotated job" covers 27 jobs with `ShutdownLeaks` / `leakcheck` lines only. The 14 Windows jobs have empty `lines` in `--json`, so nothing says they are this crash at all, and the mac `crashtest` job starred on the bug (a `mach_msg2_trap` crash, i.e. mis-starred) cannot be told apart from the rest.
  - Workaround: downloaded all 31 `live_backing.log` files and grepped `TEST-START` / `Hit MOZ_CRASH` / `PROCESS-CRASH` with a script.
- It could show: the `PROCESS-CRASH` signature and the test named in it (or the test running at the `Hit MOZ_CRASH` line) for each occurrence, and flag occurrences whose log does not contain the bug's summary text.
