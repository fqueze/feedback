## `fx-tests task` does not flag a job-wide harness breakdown, nor surface the harness traceback

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg.0 --profiles`
- Expected: for a job where 1152 of ~1200 executed tests TIMEOUT within 4 s of each other, a line
  saying the job broke down as a whole, plus the harness-level ERROR at the end of the job
  (`Following exceptions were raised:` / `PermissionError: [WinError 5] Access is denied` from
  `_winapi.CreateProcess`), which is the actual answer for every one of those tests.
- Got: the outcome counts (`1152 TIMEOUT`) and a per-test list where each entry just says
  `TIMEOUT — Test timed out`. The launch failure was only found by loading the resource-usage
  profile and reading markers at the end of the run.
- Workaround: `profiler-cli thread markers --search "Retrying,exception,Traceback" --list` on the
  resource-usage profile.

## Question: "a passing run of this config to compare with" — no way to get one

- Command: `fx-tests test <path> --task-ids` lists only failing tasks; `--coverage` gives configs,
  not tasks.
- Needed: the task ID of a passing `test-windows11-64-25h2/opt-xpcshell-msix` job that ran the
  same (full) manifest set, to compare against the failing job's resource-usage profile.
- Workaround: scripted Treeherder API calls (`/api/project/autoland/push/?id__lt=...`, then
  `/api/jobs/?push_id=...`) over ~25 pushes; the one passing job found ran only a subset of
  manifests (56 s), so it was not comparable. What would have answered: `--task-ids --passing`
  (or a few passing task IDs per config) with the number of tests the job ran.
