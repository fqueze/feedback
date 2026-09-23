## "Issues (first failure per run)" counts a TEST-KNOWN-FAIL as a run's failure, hiding that every run failed the same way

- Command: `fx-tests test browser/components/preferences/tests/home/browser_homepage_firefox_home_support_firefox.js`
- Expected: 14 runs under the timeout-threshold message, or at least a note that the 6 "changed preference" runs also carry it.
- Got: `8x This test exceeded the timeout threshold...` and `6x changed preference: browser.crashReports.cleanupCheck.lastDate`, as two failure modes. In the profile of one of those 6 (Q0y-BqG9T1yj78S9oVBk0g), the pref message is `TEST-KNOWN-FAIL`, and the `TEST-UNEXPECTED-FAIL` right after it is the timeout threshold.
- Workaround: `fx-tests task <id> --messages` on each of the 6 tasks, which showed both messages in every one: a single failure mode.

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test <path> --bugs`
- Expected: a `Bugs: none` line.
- Got: the normal output, with no bugs section at all, so I couldn't tell "no bug" from "the flag did nothing".
- Workaround: searched the Bugzilla REST API by hand.

## Question: "a job where this test passed in manifest order, to compare with a failing one"

- `--durations` gives the distribution of passing durations, but no task IDs behind them, and `--task-ids` lists only failing tasks.
- The median passing duration on the failing config is 50.5s, above the 45s limit, so I needed a passing job's timeline to see how a 58s run passes.
- Workaround: pulled 42 successful a11y-checks job IDs from the Treeherder API over 70 pushes, and ran `fx-tests task <id> --passed` on each until one had the test (QME7XZEtTN22NTQ6HLolKw).
- What would have answered it: `--task-ids --passed`, or a few task IDs per config in the `--durations` table (e.g. the ones closest to the median and the max).
