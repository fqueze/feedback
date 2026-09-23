# fx-tests feedback — browser_aboutdebugging_thisfirefox.js

## Question: "the minidump ID of the crash this job recorded for my test"

- Command: `fx-tests task AHcMg5k-SDmfpThFMLCKAw --profiles` (and `--messages`, `--json`)
- Expected: the CRASH outcome it lists (`CRASH, FAIL — 2 failing executions of 3`, `@ mozilla::(anonymous namespace)::RunWatchdog`) to come with the minidump ID that `fx-tests crash <taskId> <minidumpId>` needs, and with the `profile_shutdown_hang_<pid>.json` URL the job also uploaded.
- Got: no dump ID anywhere, `--json` only has `statusCounts.CRASH: 1`. `fx-tests test <path> --task-ids` gave the task but no minidump ID either (its help says it does "where the dump was uploaded"; here it was uploaded).
- Workaround: listed the task's artifacts from the Taskcluster queue API (`.../runs/0/artifacts`) and found `public/test_info/08ea0436-50af-4eb1-8750-b47786564c3d.dmp`.

## Question: "how often this crash signature happens tree-wide"

- Command: `fx-tests crashes --harness mochitest --limit 0 | grep RunWatchdog`
- Expected: a row for `@ mozilla::(anonymous namespace)::RunWatchdog`, which this test hit on 2026-09-18, inside the window.
- Got: no row. `fx-tests test <path>` also reports `0 crash` for the test over the window (it counts the run as the FAIL, first failure per run). So a shutdown hang that follows a first failure seems to be invisible to both counts.
- Workaround: none, the tree-wide count is left out of the report.
