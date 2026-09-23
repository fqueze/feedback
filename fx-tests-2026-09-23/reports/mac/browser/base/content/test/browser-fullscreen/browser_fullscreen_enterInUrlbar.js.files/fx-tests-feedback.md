## Question: the failing task IDs of one test on a try push

- Command: `COLUMNS=250 fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`
- Expected: every task ID behind the test's 12 failures (6 jobs), since `--limit 0` means no limit.
- Got: `… 7 more tasks` and `… 1 more profile` still truncated in the test's block; `--limit 0` does not reach the per-row task/profile lists. `--test <path> --task-ids --profiles` printed only the per-config table, no task IDs.
- Workaround: `--json` and a Python walk to find `.permaFails[i].taskIds`.
- Could have shown: the full per-row task and profile list under `--limit 0` (or under `--test <path> --task-ids`).
