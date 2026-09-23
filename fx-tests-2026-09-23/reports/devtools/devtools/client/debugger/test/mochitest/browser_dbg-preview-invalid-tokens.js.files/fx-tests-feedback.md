## `--bugs` prints nothing, not even "none found"

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-preview-invalid-tokens.js --bugs`
- Expected: a Bugs section listing bug 1958748 ("Intermittent devtools/client/debugger/test/mochitest/browser_dbg-preview-invalid-tokens.js | single tracking bug", RESOLVED FIXED), or at least a line saying no bug was found.
- Got: the same output as without `--bugs`, with no Bugs section and no message. I could not tell "no bug names this test" from "the flag did nothing".
- Workaround: took the bug numbers from the manifest's `skip-if` comment and the fix commit, then read them with `mcp__moz__get_bugzilla_bug`.
## Reviewer (review-browser_dbg-preview-invalid-tokens.js): the task IDs of the jobs where a test passed on a try push

- Command: `fx-tests try 2888bcab0070 --all-jobs --test devtools/client/debugger/test/mochitest/browser_dbg-preview-invalid-tokens.js --task-ids` (and the same with `--json`).
- Expected: the task IDs behind each "passed" count, so the resource-usage profile of a passing job can be loaded to check that no window leaked.
- Got: per-config counts only. `--task-ids` prints nothing when nothing failed, and the JSON has no task IDs either. Adding `--config debug` is refused.
- Workaround: I took the task IDs from a sibling report's Treeherder jobs dump (`todo.files/try-jobs.json`), then ran `fx-tests task <id> --passed`.
