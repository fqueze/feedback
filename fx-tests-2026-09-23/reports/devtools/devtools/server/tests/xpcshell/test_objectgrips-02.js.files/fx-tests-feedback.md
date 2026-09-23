## `fx-tests task --profiles` hides that a job melted down, and truncates the test being investigated
- Command: `fx-tests task Q6pDc4X0SYOXqgi4KIpuzA --profiles`
- Expected: a note that this job is a job-wide breakdown (476 TIMEOUTs out of 772 tests, all "failed only in the parallel phase"), and the row for the test I came from (`fx-tests test <path> --task-ids` sent me here).
- Got: the first 20 failing tests in alphabetical order, then `… 457 more (--limit 0 for all)`. The test I asked about was not shown, and nothing said the whole job had broken. I had to rerun with `--limit 0` and grep 1,900 lines.
- Workaround: `--limit 0` plus rg for the path. It could show a "job-wide: N of M tests TIMEOUT" line, and accept `--test <path>` to always include that row.

## `fx-tests test --bugs` prints nothing when no bug names the test
- Command: `fx-tests test devtools/server/tests/xpcshell/test_objectgrips-02.js --bugs`
- Expected: "no sheriff-annotated bug names this test". Ideally also the bugs sheriffs starred these jobs on, since the tracking bug here (2016044, "windows msix <random_test> … single tracking bug") names other tests.
- Got: the normal output with no Bugs section at all, which looks the same as the flag being ignored.
- Workaround: searched Bugzilla by hand.
