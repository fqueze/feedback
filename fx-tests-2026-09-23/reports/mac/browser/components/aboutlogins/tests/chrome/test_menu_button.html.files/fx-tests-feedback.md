# fx-tests feedback (test_menu_button.html report)

## Question: how often does this test fail, counting jobs killed at maxRunTime?

- Command: `fx-tests test browser/components/aboutlogins/tests/chrome/test_menu_button.html` (and `--history`, `--task-ids --limit 0`)
- Expected: the failures in the -vms opt-mochitest-chrome-1proc jobs that hit the 60-min `maxRunTime`, or at least a count of such jobs for the configs the test runs on, since per-test profiles and logs exist for them.
- Got: 10 failures, all in one confirm-failures job (C-r5OnNmSoWemihoYQ07Aw), and "test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc 314 runs 314 pass". In fact 85 autoland jobs of that config timed out 2026-09-16 .. 2026-09-21; 43 of them reached this test's manifest and failed it the same way. None of them is visible, not even as "N jobs of this config were killed and not read".
- Workaround: Treeherder `/api/project/autoland/jobs/?job_type_name=...` to list jobs with duration >= 59 min, then download every log and grep the failure line (script output in `vms_chrome1proc_autoland_jobs.txt`, `timeouts_signature.txt`). About 15 tool calls.
- What the output could have shown: under the per-config table, "K jobs of this config exceeded maxRunTime in the window (not read); per-test profiles exist in J of them", with their task IDs under `--task-ids`.

## Question: why was a -cf (confirm-failures) job the only failing one?

- Command: `fx-tests task C-r5OnNmSoWemihoYQ07Aw --profiles`
- Expected: that this is a confirm-failures task, which test it confirms, and the parent job it was spawned for.
- Got: "1 tests, 10 executions (5 of them harness reruns)", which reads like a normal job.
- Workaround: read the task definition (`MOZHARNESS_CONFIRM_PATHS`, `--repeat=4`, `tags.action=confirm-failure`), then Treeherder for the push's other jobs.
