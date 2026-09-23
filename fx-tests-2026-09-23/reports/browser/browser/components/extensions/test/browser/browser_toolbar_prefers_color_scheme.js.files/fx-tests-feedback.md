## Question: "in each failing job of my test, did test Y fail too, and how?"

- Command: 31 x `fx-tests task <id> --limit 0`, grepping for `browser_ext_commands_execute_page_action.js` (see `all-tasks-summary.txt`).
- What the output could have shown: `fx-tests test <path> --task-ids` could list, per task, the other tests that failed in it (or the first failure in the same manifest), which is the leaker-suspect question for any order-dependent failure.

## Question: "are the late failures on revisions from before the fix?"

- Command: `fx-tests test <path> --task-ids` groups tasks by the day they ran. The 3 failures of 2026-09-14 looked like post-fix recurrences; they are on autoland pushes of 2026-08-15 (backfills). Found only via the Treeherder push API.
- What the output could have shown: the revision and its push date next to each task ID, or a flag on tasks whose push is much older than the run.

## `fx-tests task` hides the test being investigated behind the default limit

- Command: `fx-tests task QpSrFmkpR1Gt5mBA-g7xLw --profiles`
- Expected: the row for the test I came from (I got the task ID from `fx-tests test <path> --task-ids`).
- Got: the first 20 failing tests alphabetically, `… 24 more`; the test and its profile URL were in the truncated part.
- Workaround: `--limit 0`. A `--test <path>` filter, or always showing the matching row, would avoid it.
