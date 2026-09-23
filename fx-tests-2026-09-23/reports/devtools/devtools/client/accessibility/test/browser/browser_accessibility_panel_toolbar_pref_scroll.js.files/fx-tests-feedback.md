# fx-tests feedback

## The task IDs of every failing run of one test on a try push

- Command: `COLUMNS=250 fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0 --full-messages`
- Expected: with `--limit 0`, every task ID and profile URL for the test's row.
- Got: the row still ends with `… 37 more tasks` and `… 16 more profiles`; `--limit 0` only lifts the row count, not the per-row lists.
- Workaround: `--json` and a script over `permaFails[].taskIds` to list the Windows tasks. The text output could have honoured `--limit 0` inside the row too, or `--test <path>` could print them.

## Whether a config family (macOS) ran the test at all on the push

- Command: `fx-tests try <rev> --all-jobs --json`, then a script searching for `macosx` job names.
- Expected: a quick way to see that the push had no macOS job at all.
- Got: had to grep the JSON; only later found `--test <path> --all-jobs` gives the per-config table. That table lists only configs that ran the test, so "no macOS row" still does not say whether the push scheduled macOS jobs. A line such as "configs on this push that did not run this test: …" or "no job on this push matches os=mac" would answer it.
