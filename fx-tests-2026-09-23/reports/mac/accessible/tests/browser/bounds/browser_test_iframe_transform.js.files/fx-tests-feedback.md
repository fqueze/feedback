## `--bugs` shows nothing for a test whose only bug is RESOLVED

- Command: `fx-tests test accessible/tests/browser/bounds/browser_test_iframe_transform.js --bugs`
- Expected: bug 1793372 ("Intermittent accessible/tests/browser/bounds/browser_test_iframe_transform.js | single tracking bug", RESOLVED FIXED in April, still receiving esr annotations), or a line saying resolved bugs are hidden / none found.
- Got: the normal output with no bugs section and nothing on stderr, so "no bug" and "bug filtered out" look the same.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=<test file name>"`.

## Question: "were the failures before or after landing X?"

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id>` for each of 13 tasks to get its revision, then fetching the test at each revision.
- The task list groups by run date but omits each task's revision (and push time), so telling which failures predate a change that landed mid-day (here bug 2069345 on 2026-09-05 06:06 UTC) needed one `fx-tests task` call per failure. A revision column in `--task-ids` would have answered it.
