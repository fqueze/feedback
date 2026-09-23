## Question: which jobs ran this test and passed, on the config where it failed once

- Command: `fx-tests test <path> --task-ids --limit 0`, `--durations`, `--coverage`
- Expected: a way to get the task ID of a passing run on the same config (test-windows11-64-25h2-ccov/opt-mochitest-plain: 67 passes, median 120 s, 1 job timing out twice at >300 s), to load that job's resource-usage profile and compare the same subtest's per-iteration time and the machine's CPU.
- Got: task IDs only for failures; `--durations` gives the pass-time distribution but not which job each came from.
- Workaround: none; the report says the comparison was not made.
- What would have answered it: `--task-ids --passing` (or `--durations --task-ids` listing the min/median/max job), limited per config.
