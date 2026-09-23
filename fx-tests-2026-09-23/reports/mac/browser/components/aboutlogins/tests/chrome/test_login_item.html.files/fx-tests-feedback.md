## `fx-tests test` silently leaves out jobs killed at maxRunTime, which here held 90% of the failures

- Question: how often does `browser/components/aboutlogins/tests/chrome/test_login_item.html` fail, and on which config?
- Command: `fx-tests test browser/components/aboutlogins/tests/chrome/test_login_item.html` (and `--history`, `--task-ids --limit 0`).
- Got: 10 failures, all in one confirm-failure job (`aXKirSbrT9GaPR8WK3sF2Q`, `-cf` config); 0 failures in 314 runs on `test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc`, and no hint that anything was missing.
- Actually: 43 jobs of that config, 2026-09-17 .. 09-21, failed this test with the same message on both the first run and the retry (86 failing runs), then were killed at 3600 s by later focus-waiting timeouts. `fx-tests task <id>` on each says "killed for exceeding its maximum duration ... this tool does not read that format", but `fx-tests test` does not count or mention them.
- Expected: at least a line in `fx-tests test` like "N jobs that run this test's manifest on <config> were killed at maxRunTime in the window and are not counted", ideally with their task IDs, so the verdict does not read "0% on vms chrome-1proc".
- Workaround: Treeherder `jobs/?job_type_name=...` for `testfailed` jobs lasting ~60 min, the task definition's `MOZHARNESS_TEST_PATHS` to keep those that run the manifest, then grepping each `live_backing.log` (`aboutlogins-chunk-failed-jobs.txt`). About 90 HTTP requests and a few minutes.

## `--config` is a prefix/substring match, so the regular config also pulls in its `-cf` sibling

- Command: `fx-tests test browser/components/aboutlogins/tests/chrome/test_login_item.html --history --config test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc`
- Expected: that configuration only (0 failures on 2026-09-17, per the per-config table).
- Got: `2026-09-17 105 pass 10 fail`, the 10 being the `...-1proc-cf` job's; nothing in the output says which configs matched.
- Workaround: none needed once noticed; printing the matched config names under the filter would make it visible.
