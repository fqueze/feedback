## Question: "a passing run of this test on the failing config, to compare with a failing one"
- Command: `fx-tests test docshell/test/navigation/test_bug279495.html --task-ids --limit 0`
- Expected: some way (e.g. `--task-ids --status pass --config <cfg>`) to get task IDs of runs where the test passed on the failing config.
- Got: only failing task IDs. No command lists passing ones.
- Workaround: Treeherder `api/jobs/?push_id=` for 3 pushes, then `fx-tests task <id> --json` on each of 13 jobs to find which chunk ran the test. About 15 calls for one passing task ID.

## `fx-tests task <id>` without the run suffix says the artifact is permanently gone
- Command: `fx-tests task DyW68xEqQg-22pOXu2Br_w --profiles` (the ID `--task-ids` printed was `DyW68xEqQg-22pOXu2Br_w.1`)
- Expected: use the run that failed, or say that run 0 was not the one that failed.
- Got: "has no profile_resource-usage.json ... Taskcluster expires task artifacts after about a month, so this is permanent", for a 2-week-old task. The hint about `.0` being assumed is buried at the end of that message.
- Workaround: pass the `.N` suffix exactly as printed.

## Android jobs: "No failing test named a per-test profile" with no next step
- Command: `fx-tests task XPJW_oFeRIuRRF_n9adOXQ --profiles`
- Expected: on Android, where no per-test profile exists and the resource-usage profile has no Gecko threads, point to `public/test_info/logcat-emulator-5554.log`. That was the only artifact with the runtime evidence (GeckoSession LocationChange order, JS errors).
- Got: the resource-usage profile URL only.
- Workaround: listed the task's artifacts with curl.
