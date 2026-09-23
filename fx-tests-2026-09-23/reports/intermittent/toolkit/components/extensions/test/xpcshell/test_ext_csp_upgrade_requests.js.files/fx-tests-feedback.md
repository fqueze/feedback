## Question: on which configs does one failure mode happen, with counts?

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_csp_upgrade_requests.js --task-ids --limit 0 --issue 1`
- Expected: the "Failing configurations" table restricted to issue 1 (TIMEOUT), so the Configs line of a report can be filled for the diagnosed mode (here 205 Android jobs vs 4 desktop; the mac-only failures are a different mode, the shutdown-hang crash).
- Got: `--issue 1` filters only the task-id list; the config table above it still mixes all modes.
- Workaround: awk over the task-id lines, stripping the chunk suffix, to count jobs per config.

## Question (review-test_ext_csp_upgrade_requests.js): what did the test log before an Android timeout whose log replay is empty?

- Command: `fx-tests task SbqcbHitRpWx0doA_3n1Sg --profiles`
- Expected: a pointer to where the test's output can still be found when the harness's `Begin/End of full log` replay holds only the force-kill ERROR. On Android that is the task's `public/test_info/logcat-emulator-5554.log`, whose `I Gecko : {"action":"test_status",...}` lines hold every assertion.
- Got: only "No failing test named a per-test profile in this job". The logcat is not mentioned, so the report concluded that Android timeouts have no log output at all.
- Workaround: list the task's artifacts with curl, fetch the 280 MB logcat, and rg for the test's `test_status` lines.

## Question (review-test_ext_csp_upgrade_requests.js): which manifest did each failure run in?

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_csp_upgrade_requests.js --task-ids --issue 1 --limit 0 --json`
- Expected: the manifest (`xpcshell.toml` vs `xpcshell-remote.toml`) per failing execution. This test runs under both on Android, with in-process and with remote extensions, and all the Android failures checked were the in-process run.
- Got: `taskIds` entries have jobName, chunk and status, but no manifest or group.
- Workaround: read the manifest from each task's resource-usage profile `test` markers, or from the logcat.
