## Test log markers of a plain mochitest are not on the parent main thread

- Command: `profiler-cli thread markers --session <s> --category Test --search test_basic_creditcard --list --limit 0`, with t-0 (parent GeckoMain) selected.
- Expected: the test's log, as for browser-chrome tests.
- Got: 0 markers. For mochitest-plain, the TEST-PASS/FAIL markers are on the test's content process (e.g. `https://example.com`), and the parent only has `SpecialPowers` Text markers.
- Workaround: `profiler-cli profile markers --search TEST-` to find the thread, then `--thread t-N`.
- Also: every `INFO` Log marker there shows `Level: (empty)`, `Message: (empty)` in both `--list` and `marker info`. The info() messages (e.g. "expecting a popup") had to come from the task log instead.

## Question: average machine CPU and IO wait over a time range of a resource-usage profile

- Commands: `profiler-cli counter list` gave "No counters in this profile"; `profile info` gave "CPU activity over time: No significant activity."
- The data is in 100 ms `CPU Use` markers (`cpuPercent`, `iowait_pct`, ...). That IO wait jumped to 35-42% during the failing test, against about 1% job-wide, was the key observation.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script averaging `iowait_pct` over ranges.
- What would have answered it: a summary of CPU Use (mean/max of cpu, user, system, iowait) for the current zoom, e.g. in `profile info` or `thread markers --search "CPU Use"` stats.
- Minor: `--search "name:IO"` also matches `NetIO`.
