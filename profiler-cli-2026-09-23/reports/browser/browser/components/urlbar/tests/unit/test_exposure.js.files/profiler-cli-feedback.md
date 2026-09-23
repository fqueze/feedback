## Question: "was the machine CPU-saturated while this test ran?" (resource-usage profile)

- Command: `profiler-cli profile info --session browser-test_exposure.js-1` on an xpcshell `profile_resource-usage.json`
- Expected: a machine-CPU summary over time, since that profile's whole point is machine CPU (`CPU Use` markers with `cpuPercent`).
- Got: `CPU activity over time: No significant activity.` The profile holds no samples, only ~29,000 `CPU Use` markers, and neither `profile info` nor `thread markers --search "name:CPU Use"` aggregates their `cpuPercent` field. I exported `--list --limit 0 --json` and averaged it in Python, per minute and over each test's `test` marker window.
- Could have shown: `profile info` summarizing `CPU Use` markers the way it summarizes sample CPU. Or a `thread markers --stat cpuPercent` / `--group-by` numeric aggregate that respects the zoom, so "zoom push m-127; thread markers --search 'CPU Use' --stat cpuPercent" answers directly.
