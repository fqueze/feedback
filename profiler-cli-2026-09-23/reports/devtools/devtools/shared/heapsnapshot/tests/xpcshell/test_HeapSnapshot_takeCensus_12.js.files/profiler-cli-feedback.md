## Question: "What was the machine's CPU use over time, e.g. per 5 s, during the 45 s this test waited?"

- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0 --session <s>` on the resource-usage profile of task PyUxuOpdQj2b3T3XyQOKpg (`profile_resource-usage.json`).
- Expected: a time-bucketed summary of the `CPU Use` markers' `cpuPercent`, like `counter info`'s "over time" section. `counter list` says "No counters in this profile".
- Got: 1,232 individual markers, one per ~100 ms, each with its own percentage.
- Workaround: `--json`, then a Python script averaging `data.cpuPercent` per 5 s bucket. A `--group-by time:5s` or a synthesized CPU counter from `CPU`-type markers would answer it.

## Question: "How long after each test's start did the harness log `<id> failed or timed out, will retry.`?"

- Command: `profiler-cli thread markers --search "will retry" --list --limit 0` and `--search name:test --list --limit 0`, same session.
- Expected: some way to see, per test, the gap between its `test` marker start and the INFO naming it. This is what separates "failed to launch" (tens of ms) from "ran and timed out" (45 s).
- Got: two flat lists of 1,476 and 2,843 markers; the INFO markers carry the test id only inside their message text.
- Workaround: `--json` for both, join on the regex-extracted id in Python.
