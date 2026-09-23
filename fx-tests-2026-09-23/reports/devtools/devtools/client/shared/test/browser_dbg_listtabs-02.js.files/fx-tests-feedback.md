## Question: which task IDs, on which config, are behind one test's failures on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --task-ids --profiles --messages --limit 0`
- Expected: with `--limit 0`, every task ID for the row, each with its config, plus both per-test profiles (first run and `-2` retry) for each task.
- Got: the row showed 5 task lines, then `… 37 more tasks`, and 5 profile pairs, then `… 16 more profiles`. `--limit 0` did not lift either cap. The profile list also had only the first-run profile per task. `fx-tests task <id> --profiles` does show the `-2` retry profile.
- Workaround: `--json`, then a script over `permaFails[].taskIds` (which has `jobName`), then `fx-tests task` for the retry profile.

## `try --test ... --all-jobs` refuses `--config`

- Command: `fx-tests try <rev> --test <path> --all-jobs --config opt-mochitest-devtools-chrome-5`
- Expected: to read only that config's passing jobs, to check the one run the default output left unread.
- Got: exit 1, "--config cannot be applied to try". Reading all jobs took a few minutes. With `--test`, a per-config filter would only narrow the rows, not change what they mean.
