## `fx-tests test --task-ids` prints the Android job list twice

- Command: `fx-tests test netwerk/test/unit/test_bug1940663.js --task-ids --limit 0` (and the same with `--issue 1`)
- Expected: 40 jobs listed once (header says "Task IDs (40 jobs)"; with `--issue 1`, "22 jobs").
- Got: the 16 Windows jobs, then the 22 Android jobs, then the same 22 Android jobs a second time (dates restart at 2026-09-02). With `--issue 1` the 22 Android jobs are listed twice. Looks like the list is concatenated per platform group and one group is appended twice.
- Workaround: ignore the repeated block.

## Question: "a passing job of the same config, near a failing one, to compare against"

- Need: to compare a failing Android job's resource-usage profile with a passing job's (same config, same or nearby push). No per-test profile exists for a pass, so this is the only baseline.
- Commands tried: `fx-tests test <path> --task-ids` (failures only), `fx-tests task <id>` (one job). Neither lists passing jobs.
- Workaround: Treeherder REST (`/api/project/autoland/push/?revision=<rev>`, then `/api/jobs/?push_id=<id>`), filtered with a Python script to find `CkLz10qwTB-1s1igHMl4Pg.0` (same push, a passing opt-geckoview-xpcshell-nofis job).
- What could answer it: something like `fx-tests task <failing taskId> --siblings`, or `fx-tests test <path> --passing --config <cfg> --limit 3`, printing task IDs and resource-profile URLs of passing jobs on the same push or nearby pushes.
