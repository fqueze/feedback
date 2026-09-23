## Question: "did passing runs on another config also hit the same ~30 s stall?"

- Command: `fx-tests test netwerk/test/unit/test_webtransport_stop_sending.js --durations --json --config macosx1015`
- Wanted: how many passing runs on macOS 10.15 took about 30 s or more (its max is 34.3 s debug / 32.2 s opt, against a p95 of 3.9 s / 2.3 s), to tell whether the same stall happens there and resolves by itself at ~30 s, below a larger timeout.
- Got: only min / median / p95 / max per config; the JSON has no per-run durations either.
- What would have answered it: a count of passing runs above a threshold (`--slower-than 20s`), or the task IDs of the slowest passing runs, whose resource-usage profiles would then show the timeline.

## Question: "what happened to this test in each failing job, in time order?"

- Needed a script (load each task's resource-usage profile, `thread markers --search <test> --list --json`, filter the test's own INFO lines) to get, per failing job, when `asyncConnect` returned and when the session became ready. Seven jobs, one load each.
- What would have answered it: `fx-tests task <taskId> --test <path> --log` printing that test's log lines from the resource-usage profile with job-relative timestamps, or the same for all failing tasks of one test.
