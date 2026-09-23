## `fx-tests task <id>` defaults to run .0 and calls a missing artifact "permanent"

- Command: `fx-tests task e6fPXv0dTUWlpLrNpuNcMw --profiles`
- Expected: the run `fx-tests try` had just listed (`e6fPXv0dTUWlpLrNpuNcMw.1`), or a note that run .0 has no profile but run .1 does.
- Got: "task e6fPXv0dTUWlpLrNpuNcMw.0 has no profile_resource-usage.json ... Taskcluster expires task artifacts after about a month, so this is permanent". The push is one day old; run .0 simply never uploaded one.
- Workaround: pass `.1` explicitly.

## Question: "did this test pass on this config anywhere in the push, or was the missing run just not read?"

- Command: `fx-tests try 2888bcab0070 --all-jobs` shows `6/7` for the test, and `… 1 more task`.
- The text does not say what the 7th run was. Only `--json` (`outcomes.notAnalyzed: 1`, `totalJobs: 4`) showed it was an unread job, not a pass. `--test <path>` then gave the per-config table, which answered it directly; a pointer to `--test` next to an `x/y` row where y-x runs are unaccounted for would have saved the detour.
