## Question: "which jobs failed because of this test?" — a harness-level ERROR attributed to the test is invisible

- Command: `fx-tests test toolkit/crashreporter/test/unit/test_crash_moz_crash.js` (also `--history`, `--task-ids --limit 0`)
- Expected: the failures from bug 2064429. The job fails on `ERROR - test_start for toolkit/crashreporter/test/unit/test_crash_moz_crash.js logged while in progress.`, and two jobs in the window (VOvmzWImRB-dq1DgOuNcXg on 2026-09-18, Ot6pdcwPT8uow8cSqwN1Nw on 2026-09-21) failed that way.
- Got: `5,153 runs 5,153 pass (100.00%) 0 fail`, "Verdict: passing", and no task IDs. The first run timed out, but the harness never logged its `test_end` (the timeout cleanup raised), and the retry passed. So the only record is a log ERROR plus a CRASH, and neither counts against the test.
- `fx-tests task Ot6pdcwPT8uow8cSqwN1Nw --profiles` does not list the test at all among its 23 failures. `fx-tests task VOvmzWImRB-dq1DgOuNcXg` lists it only as "CRASH — 1 failing execution of 2, Passed when the harness reran it. [Unknown]". Neither shows the ERROR line that actually turned the job orange. The VOvmz entry also has no profile link, although `public/test_info/profile_test_crash_moz_crash.js.json` exists (the failure message that would have named it was never logged).
- Workaround: read the resource-usage profile (`thread markers --search <test> --list`) and the live_backing.log, and query Treeherder's `failuresbybug` API for the full occurrence list.

## Question: "what failed in the jobs sheriffs starred on this bug?" — `intermittent --bug` shows other failures

- Command: `fx-tests intermittent --bug 2064429 --full-messages`
- Expected: the failure the bug is about (the "logged while in progress" ERROR for this test).
- Got: "Failure messages, per annotated job: 2x test_default_profile_does_not_exist - [...] 11 == 21" and "Tests named: test_backgroundupdate_exitcodes.js". Those are the jobs' first failure lines, a different test entirely. It read as if sheriffs had starred unrelated failures on the bug, but both jobs did contain this bug's failure.
- Could have shown: the failure line matching the bug summary, or a flag saying the listed messages are the job's first failure, not necessarily the annotated one.
- Also, the window is 7 days, with no flag to widen it. The bug has 14 annotations since 2026-08-18 (Treeherder `api/failuresbybug`), which I needed for the config breakdown.
