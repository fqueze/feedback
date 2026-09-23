## `fx-tests test <path> --bugs` prints nothing about bugs when none matches

- Command: `fx-tests test gfx/layers/apz/test/mochitest/test_relative_update.html --bugs`
- Expected: a Bugs section listing bugs naming the test, or a line saying none was found (and which bugs were searched: open only, or closed too).
- Got: output identical to the run without `--bugs`; no Bugs section, no "none" line, so "no bug" and "flag ignored" look the same.
- Workaround: Bugzilla REST search `rest/bug?summary=test_relative_update`, which found only closed bugs (1589541, 1643042, 1664587).
- Question the default output did not answer: "is there an open bug for this failure?"

## No way to find a per-test profile that holds a passing run of a test

- Question: "where is a profile of this test passing, to compare with the failures?" The harness retry passed and left no profile.
- Commands: `fx-tests test gfx/layers/apz/test/mochitest/<each later test in mochitest.toml> --task-ids --limit 0`, then `fx-tests task IbKLawMaRn-PwnIsE5FbxA --profiles`; that job's `profile_test_wheel_scroll.html.json` held a PASS of test_relative_update.html earlier in the same browser.
- What could have shown it: `fx-tests test <path> --profiles` also listing failure profiles of tests that ran after it in the same browser session, marked "contains a passing run".
(Also logged, misfiled, at the end of profiler-cli-feedback.md.)
