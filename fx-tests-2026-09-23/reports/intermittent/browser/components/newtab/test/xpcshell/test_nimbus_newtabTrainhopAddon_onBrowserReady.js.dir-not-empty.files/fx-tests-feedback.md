## Question: on which configs and build types does one failure mode happen? (test_nimbus_newtabTrainhopAddon_onBrowserReady.js, NS_ERROR_FILE_DIR_NOT_EMPTY)

- Command: `fx-tests test <path> --task-ids --issue 2 --limit 0`
- Expected: a per-config (and opt/debug) count for issue 2, next to the config's run count. Here all 273 are opt and none debug, which is a key clue.
- Got: the per-config table is for all modes; the issue's task list is per day, one line per task.
- Workaround: `--json`, then a script counting `taskIds[].jobName` against `configs[].runCount`.
- What would have answered it: the failing-configurations table restricted by `--issue N`.

## Question: which failing tasks of one failure mode have a per-test profile?

- Command: `fx-tests test <path> --task-ids --issue 2 --limit 0`, then `fx-tests task <id> --profiles` one task at a time.
- Expected: a marker on each task line saying whether a `profile_<test>.json` exists.
- Got: nothing; this mode's cleanup error never uploads a profile by itself, so 186 of 273 tasks have none.
- Workaround: a script listing each task's Taskcluster artifacts (`artifacts-per-task.txt`).
- What would have answered it: `--task-ids --profiles` printing the per-test profile URL, or "none", per task.
