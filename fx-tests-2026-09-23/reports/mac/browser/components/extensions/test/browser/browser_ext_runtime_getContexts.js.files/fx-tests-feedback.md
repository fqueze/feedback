## Question: which test in this job failed first, in time order (the leaker suspect)

- Command: `fx-tests task <taskId> --profiles --limit 0` (and `--json`), for 38 jobs of a cascade where ~44 tests fail with "vsync remained enabled".
- Expected: the failures in execution order, or a start time per failing test, so the first failure of the cascade (the leaker suspect) is the first row.
- Got: failures sorted alphabetically by path, no timestamps in the text or the JSON. Alphabetical order only matches execution order within one manifest.
- Workaround: loaded the resource-usage profile for one job to get the chronological `test` markers; for the other 37 jobs, a script over `--json` taking the alphabetically-first failing test in the manifest's directory, which is only valid because the whole cascade is in one manifest.
- What would have answered it: an execution-order sort (or a `start` field per failure in `--json` and a "first failure in the job" line in the text output).

## Question: same as above — the ordering of `failures` is not what it looks like

- Command: `fx-tests task <taskId> --json --limit 0`, taking `failures[0]` within one manifest's directory as the first failure.
- Expected: from the text output, which looked alphabetical, `failures` in path order.
- Got: `failures` is ordered by failure count first, then path. A test that failed on both the run and the retry (2/2) comes before an alphabetically earlier test that failed once (1/2). So in 4 jobs my script named `browser_ext_incognito_views.js` as the first failure instead of `browser_ext_commands_execute_page_action.js`, and I nearly reported a second leaker.
- Workaround: looked the leaker up by name in every job instead of taking the first row.
- What would have answered it: document the sort order in `--help`, or add a per-failure start time so the order can be chosen.

