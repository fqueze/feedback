## A test failing in 21 annotated jobs reads as "passing, 0 fail"

- Command: `fx-tests test dom/base/test/fullscreen/test_fullscreen-api-race.html` (and `--config aarch64-vms --history`).
- Expected: the 21 failures sheriffs starred on bug 1815164 (2026-09-16 to 09-21, all `macosx1500-aarch64-vms`), or at least a warning that some jobs of this config have no results.
- Got: `Verdict: passing. 6,773 of 6,773 runs passed.` Every failing job was killed at its max run time (the focus loss makes each test wait ~300 s), so none of them published results. The only sign was a drop in daily pass counts on the vms config from 09-16 on (17, 5, 9, 1, 3, 9 against about 20 a day before).
- Workaround: `fx-tests intermittent --bug 1815164` for the task IDs, then Treeherder's jobs API for per-day result counts of the config.
- What would have answered it: counting killed (`max run time exceeded`) jobs of each config that schedules the test, next to its runs, as "N jobs of this config were killed and have no results".

## `fx-tests task --profiles` refuses a killed task that does have per-test profiles

- Command: `fx-tests task Puh1BpkdTeC0Tpq7kjIl2Q --profiles` (same for 6 other tasks of bug 1815164).
- Expected: the per-test failure profiles.
- Got: `task ... was killed for exceeding its maximum duration, so its profile is a partial stream ... there are no per-test results to read.` The task has 14 `profile_test_*.json` artifacts, including `profile_test_fullscreen-api-race.html.json` and `-2`, plus `mochitest-plain_errorsummary.log`.
- Workaround: the Taskcluster artifacts API and the errorsummary log.
- What would have answered it: list the `profile_*.json` artifacts, and the failures from the errorsummary, when the resource-usage profile is unreadable.
