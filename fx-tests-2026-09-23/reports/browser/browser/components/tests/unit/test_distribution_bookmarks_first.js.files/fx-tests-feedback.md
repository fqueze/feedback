## `--since` takes a number of days, the guide does not say so

- Command: `fx-tests test browser/components/tests/unit/test_distribution_bookmarks_first.js --task-ids --limit 0 --issue 1 --since 2026-09-20`
- Expected: the failing tasks since that date (the guide's THE WINDOW section says "--day and --since filter", and `--day` takes a date).
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-20"`.
- Workaround: `--since 2`. Accepting a date too, or saying "days" in the guide, would avoid the retry.

## Question: "is there a bug naming this test?" — `--bugs` prints nothing when the answer is none

- Command: `fx-tests test browser/components/tests/unit/test_distribution_bookmarks_first.js --bugs`
- Expected: a line such as "Bugs: none name this test".
- Got: the same output as without `--bugs`; only `--json` (`annotatedBugs: []`) said "none".
- Workaround: `--json`, plus a Bugzilla REST search to be sure.
