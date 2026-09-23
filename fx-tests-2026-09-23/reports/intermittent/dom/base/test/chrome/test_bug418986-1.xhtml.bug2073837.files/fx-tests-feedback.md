## Jobs killed at max-run-time are invisible, so a near-perma failure reads as 0% on its config

- Command: `fx-tests test dom/base/test/chrome/test_bug418986-1.xhtml` (and `--config test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc --history`)
- Expected: the 19 `mochitest-chrome-1proc` jobs sheriffs starred on bug 2073837 (2026-09-19/20) counted as failures of this config, or at least a line saying N jobs of this config were killed without a readable profile.
- Got: `test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc 376 runs 376 pass 0 fail`; the only failures shown are the 6 reruns of one `-cf` job. `fx-tests intermittent --bug 2073837` shows 22 annotated jobs, so the two commands disagree without saying why.
- Workaround: `fx-tests intermittent --bug` for the task IDs, then the Taskcluster artifacts API.

## `fx-tests task` refuses a killed task although its per-test profiles exist

- Command: `fx-tests task QVroiDtfQn6CDTendUZN9g --profiles`
- Expected: the per-test failure profiles (`public/test_info/profile_test_*.json` are all uploaded, 11 of them) even if the resource-usage profile is a partial stream.
- Got: "was killed for exceeding its maximum duration ... there are no per-test results to read", and no profile URLs.
- Workaround: `curl .../task/<id>/runs/0/artifacts` and pick the `profile_test_*.json` names by hand.

## Question: "the passing runs of this config in the same time window, with their task IDs and workers"

- Needed to compare pass vs fail tasks (worker, VM boot time, build) for an environment failure.
- Commands: `treeherder-cli --repo autoland --similar-history <job id> --json`, then the Treeherder `/jobs/<id>/` API for task IDs and machine names, then Taskcluster `status` per task.
- What could have shown it: `fx-tests test <path> --config <c> --task-ids --passing` (or `--coverage --task-ids`) listing passing task IDs, workers and start times per day.
