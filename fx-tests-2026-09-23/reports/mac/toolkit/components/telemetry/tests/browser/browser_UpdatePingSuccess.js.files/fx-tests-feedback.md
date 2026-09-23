## `task --profiles` does not list the harness's hang profile for an application timeout

- Command: `fx-tests task YQwCOM0hTMCogZNuGfIcOg.0 --profiles`
- Expected: for the `application timed out after 370.0 seconds with no output` entry of browser_UpdatePingSuccess.js, the URL of the profile the harness captured on the hang (it logs "profiler Sending SIGUSR1 to pid 5915 ..."), uploaded as `public/test_info/profile_0_5915.json`.
- Got: the TIMEOUT entry with no profile line, so it looked like there was nothing to load for this test; only the resource-usage profile and another test's profile were listed.
- Workaround: listed the task's artifacts with `curl .../runs/0/artifacts` and found `profile_0_<pid>.json`. That profile had the whole answer (main thread stuck in `GleanString.testGetValue` for 6 min). Same for GgGg4A34Qs2qTUPrpYk7fg.0 (`profile_0_4451.json`).

## Question: "what was the hung parent's main thread doing" (Windows, no profile)

- Command: `fx-tests task BkoZ5pxrQo6uhNOPUrMetg.0 --profiles`
- Got: `CRASH, TIMEOUT — 6 failing executions of 6` and `[Unknown]`, which reads as six failing runs or crashes. They are the harness's minidumps of each hung process after one timeout, all "No crash". No minidump ID was shown, so `fx-tests crash` was not usable.
- Workaround: parsed `mochitest-browser-chrome_errorsummary.log` with a Python script to pull `stackwalk_stdout` of the dump containing `block_on_dispatcher`, and its threads.
- What the output could have shown: count the hang dumps as one timeout, not six executions, and say "hang dumps of N processes (no crash)"; give the parent's main-thread top frames, or a minidump ID usable with `fx-tests crash --all-threads`.
