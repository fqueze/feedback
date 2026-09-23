## `test --coverage` truncates the one row that matters

- Command: `fx-tests test devtools/client/netmonitor/test/filters/browser_net_filter-value-preserved.js --coverage`
- Expected: the config that "only ever skipped" the test, with its skip reason, since the summary line counts it ("States: 25 ran, 1 only ever skipped").
- Got: the table sorted by runs, truncated at 10 rows. The skipped config (0 runs) came last, so it was cut off.
- Workaround: re-ran with `--limit 0`. Suggestion: always show skipped or never-ran rows regardless of the limit, or sort them first.

## `try --test` cannot say whether the skipped config was scheduled

- Question: "did the try push schedule the config where this test is skipped (http3)?"
- Command: `fx-tests try 2888bcab0070 --all-jobs --test <path>`
- Got: only the configs that ran the test. If a config was never scheduled, it looks the same as one where the test was filtered out.
- Workaround: read the parent's Treeherder job list (`todo.files/try-jobs.json`). Suggestion: when the test has `skip-if`/`run-if` conditions, show the configs on central that match them, and whether the push had a job for each.
