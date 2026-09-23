## Failure message of one test on a try push
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/debugger/test/mochitest/browser_dbg-pretty-print-sourcemap.js --profiles --task-ids --messages`
- Expected: the per-config table plus the failure message(s), task IDs and profile URLs for that test.
- Got: only the per-config ran/failed table; `--profiles`, `--task-ids` and `--messages` printed nothing. Without `--test`, the test's KNOWN INTERMITTENTS row listed task IDs and profiles but no message, even with `--messages`.
- Workaround: `fx-tests task <taskId> --messages --full-messages` on each failing task.

## `--bugs` listed no bug
- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-pretty-print-sourcemap.js --bugs`
- Expected: bug 1817966 ("Intermittent devtools/client/debugger/test/mochitest/browser_dbg-pretty-print-sourcemap.js | single tracking bug"), which the skip-if annotations also name.
- Got: the normal `test` output with no bug section, and no line saying none were found.
- Workaround: took the bug number from the `skip-if` comment in the manifest.
