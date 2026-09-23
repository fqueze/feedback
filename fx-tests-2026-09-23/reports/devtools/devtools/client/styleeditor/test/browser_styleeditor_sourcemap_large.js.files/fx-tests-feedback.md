## Question: did this un-skipped test fail every time it ran on the config its skip-if covered?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids` (and `--all-jobs`)
- Expected: `browser_styleeditor_sourcemap_large.js` under PERMA-FAILS for `test-linux2404-64/opt-mochitest-devtools-chrome-a11y-checks-5`: it failed 6 of 6 readable executions (3 jobs x first run + retry).
- Got: listed under NEW INTERMITTENTS as `6/7`. The 7th "run" is job `e6fPXv0dTUWlpLrNpuNcMw.0`, which has no profile (`outcomes.notAnalyzed: 1` in `--json`), so an unread job turned a perma-fail into an intermittent. The same happened to its 5 siblings in that chunk.
- Workaround: read `outcomes` in `--json` (`failedTwice: 3, notAnalyzed: 1`). The default output could exclude unread jobs from the denominator, or say "6/6 read, 1 job not read" next to the rate.

## `task <id>` defaults to run .0 when the push listed .1

- Command: `fx-tests task e6fPXv0dTUWlpLrNpuNcMw --profiles`, taken from `task e6fPXv0dTUWlpLrNpuNcMw.1` in the `try` output.
- Expected: the run that `try` listed, or the latest run.
- Got: exit 4, "has no profile_resource-usage.json" for `.0`. The message does say to check the retry number, so it cost only one call.
