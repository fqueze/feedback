## Question: which subtest (and last step) timed out in each failing run of one test

- Command: `fx-tests test browser/base/content/test/browser-keyboard/browser_toolbarKeyNav.js --task-ids --limit 0` and `fx-tests task <id> --messages`
- Expected: for a TIMEOUT, the subtest name the harness logs (`TEST-FAIL | <path> | testArrowsDisabledButtons - Test timed out`), so the Issues list can tell apart timeouts in different subtests.
- Got: every row is just `Test timed out` / `Test exceeded time limit`; the subtest name is dropped, so 42 timeouts look like one failure mode without any way to check it.
- Workaround: downloaded each task's `live_backing.log` and grepped the `Entering test` / `Listening on item` / `Test timed out` lines (42 logs). All 42 turned out to be the same step, but that was not knowable from the tool.
- What the output could show: the subtest prefix of the timeout message (it is in the log line), grouped under Issues.

## Question: the per-test profile URLs of a job that was killed for exceeding its max run time

- Command: `fx-tests task OAdKPMzLS4OqRbfu5Aoyuw --profiles`
- Expected: the per-test failure profiles, which were uploaded (the log says `profile uploaded in profile_browser_toolbarKeyNav.js.json` and `profile_browser_toolbarKeyNav-2.js.json`; both URLs answer 200).
- Got: `task OAdKPMzLS4OqRbfu5Aoyuw.0 was killed for exceeding its maximum duration, so its profile is a partial stream ... there are no per-test results to read.` and nothing else.
- Workaround: grepped the task log for `profile uploaded in` and built the artifact URLs by hand.
- What the output could show: the `profile uploaded in` names from the log, as artifact URLs, even when the resource-usage profile can't be read.
