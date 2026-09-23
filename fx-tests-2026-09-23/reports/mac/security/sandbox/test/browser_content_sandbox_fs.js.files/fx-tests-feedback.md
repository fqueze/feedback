## Question: every failing task ID and per-test profile URL of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`
- Expected: `--limit 0` to list all 12 task rows and all 6 per-test profiles of `security/sandbox/test/browser_content_sandbox_fs.js`.
- Got: the per-test block still ends with `… 7 more tasks` and `… 1 more profile`; `--limit 0` only removes the limit on the number of tests, not on the rows inside each test. Nothing in the text output names the flag that would expand them.
- Workaround: re-ran with `--json` and walked `taskIds` / `profiles` with a Python snippet.
- What would have answered it: `--limit 0` (or a `--test <path>` filter on `try`) printing every task and profile of the matching test, or the `try` output pointing at `fx-tests task <id> --profiles` per task.
