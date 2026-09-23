## Question: in each failing job of this test, which test failed first in the same browser, and did it fail the same way?

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id> --json` for each of the 31 tasks, and a script checking whether `browser_ext_commands_execute_page_action.js` had `No anchor node` in each.
- Expected: for a failure that cascades (here `waiting for vsync to be disabled`, which 143 tests share), some way to see the first failing test before this one in the same job next to each task ID.
- Got: task IDs and configs only; each task had to be fetched and parsed separately (31 calls).
- Workaround: the loop above (tasks-summary.txt in this directory).
- Could show: a `--first-failure-in-job` (or similar) column in `--task-ids`: the earliest failing test of the same manifest in that job, with its first message.

## Question: is the fix present at the revision each failing task ran?

- Command: 31 `curl https://hg.mozilla.org/.../raw-file/<rev>/<path> | rg -c awaitPageActionButton` plus 31 `json-rev` calls for push dates.
- Expected: `--task-ids` to carry each task's revision and push date, so "all failures predate landing X" is checkable without leaving the tool.
- Got: the revision is only in `fx-tests task`; the push date nowhere.
- Workaround: the loop above (task-revs.txt in this directory).
