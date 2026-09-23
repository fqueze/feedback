## `fx-tests test` shows 0 failures for a test whose only failure mode is a harness no-output timeout
- Command: `fx-tests test dom/media/test/test_eme_wv_privacy.html` (also `--history --config android-em`, and `fx-tests test dom/media/test/mochitest_eme.toml`)
- Expected: the 17 jobs sheriffs starred on bug 2069126 (2026-09-02 … 09-04), whose failure line is `TEST-UNEXPECTED-FAIL | dom/media/test/test_eme_wv_privacy.html | application timed out after 370 seconds with no output`, counted as failures (or timeouts) of this test.
- Got: "Verdict: passing. 10,596 of 10,596 runs passed", 0 fail on every day of the burst. `fx-tests task <id>` on those jobs says "0 failing" and only warns about `CRASH dom/media/test/mochitest_eme.toml`. `fx-tests crashes --path dom/media/test` does not list the manifest-level crash either.
- Workaround: `fx-tests intermittent --bug 2069126 --since 30 --tree all` found the jobs. The question the default output could have answered: "has this test had harness-level timeouts, and when".
## No way to get the task IDs of a test's passing runs on one push (review-test_eme_wv_privacy.html.bug2069126)
- Question: "the passing runs of this test on the same push as failing task eMYwGnJ1SxaDxBsPIVH5gg, with their full task IDs" (to read a passing run's logcat; the report cites 8-character prefixes such as `D_FwgXgG`).
- Command: `fx-tests test dom/media/test/test_eme_wv_privacy.html --coverage --task-ids`; `fx-tests task D_FwgXgG`.
- Expected: task IDs of passing runs per config (or per push), or `fx-tests task` resolving a unique prefix.
- Got: `--task-ids` lists only failing tasks; `--coverage` gives counts only; a prefix is rejected (HTTP 400).
- Workaround: Taskcluster `task-group/<group>/list` walked with a script, matching the prefix. Found `D_FwgXgGRhWId9FRRPX13w`. Could not do the same for the ccov cron jobs (`T6tHNfAp`, …), whose group is not in the pushdate index.
