## `Issues (first failure per run)` counts a TEST-KNOWN-FAIL as a run's first failure

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_history_sidebar_glean.js`
- Expected: one failure mode, `Menu item is hidden`, 19x. It is in every failing run.
- Got: two modes, `Menu item is hidden` 14x and `FAIL handleEvent() was unable to perform a11y checks on hidden node` 5x. In the profile of one of those 5 (RG0rUdrDRsW9c01hYUPInQ), the a11y message is `TEST-KNOWN-FAIL`, and the real failure comes later in the same run. `fx-tests task <id> --messages` lists both messages for all 5 runs, without their status.
- Cost: I loaded a profile to find out whether issue 2 was a second failure mode.
- Workaround: `fx-tests task <id> --messages` for each issue-2 task, then the test log in a profile.

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test <path> --bugs`
- Expected: a line like `Bugs: none name this test`.
- Got: the normal output with no bug section, so "no bug" looks the same as "flag ignored". `--json` has `annotatedBugs: []`.
- Workaround: check `--json`, then a Bugzilla quicksearch.
