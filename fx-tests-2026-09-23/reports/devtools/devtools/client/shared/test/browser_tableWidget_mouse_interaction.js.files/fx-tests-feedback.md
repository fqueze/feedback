## Question: which tasks ran this test on config X and passed?

Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/shared/test/browser_tableWidget_mouse_interaction.js --all-jobs --task-ids --profiles`

Expected: the task IDs (and resource-usage profile URLs) behind each `passed` count, so a
"no longer fails" verdict can cite the passing runs.

Got: only the per-config counts table; `--task-ids` and `--profiles` are silently ignored with
`--test`, and `--json` has no task IDs either.

Workaround: `fx-tests try <rev> --task-ids --limit 0`, grep the failed tasks of the config
(failing for other tests), then `fx-tests task <id> --passed --limit 0` to find this test's PASS.
That only reaches jobs that failed for another reason; green jobs' IDs stay unreachable.

## Job count differs between the default and --all-jobs `--test` views

Commands: the same `fx-tests try <rev> --test <path>` with and without `--all-jobs`.

Expected: the same `jobs` column (it is described as exact in the default view).

Got: `test-linux2404-64/opt-mochitest-devtools-chrome-5` shows 4 jobs without `--all-jobs`,
3 with it. The default try output lists only 2 distinct failed tasks for that config
(b9m3T5-qSPG3d0ItqVggcw, Q8SXsj1rQoWJKYkWw2zwog).

## `--config` refused on `fx-tests try`

Command: `fx-tests try <rev> --task-ids --limit 0 --config test-linux2404-64/opt-mochitest-devtools-chrome-5`

Expected: the failed tasks of that one config.

Got: an error; `--config` is listed in `fx-tests try --help` but refused. Workaround: full
output plus grep.
