## `test --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/base/content/test/popups/browser_popup_new_window_size.js --bugs`
- Expected: a "Bugs: none found" line (or a list).
- Got: the normal output with no bugs section at all, so "no bug names this test" is indistinguishable from "the bugs query did not run / failed silently".
- Workaround: none; assumed none.

## No way to list the passing runs (task IDs, revisions) of a test on one config

Question: "what is the first revision on the failing config where the test passed after it stopped failing?" (to find the landing that stopped it).
- Command: `fx-tests test <path> --config windows11-64-25h2/debug-mochitest-browser-chrome-standalone --day 2026-09-10 --task-ids --json`
- Got: only failing tasks in `taskIds`; passing runs are counted but not listed, so the stop can only be dated to a day, and bracketed with hg pushlog by hand.
- Would help: `--task-ids --all` (or `--passes`) listing passing task IDs with revisions/push times for one config.
