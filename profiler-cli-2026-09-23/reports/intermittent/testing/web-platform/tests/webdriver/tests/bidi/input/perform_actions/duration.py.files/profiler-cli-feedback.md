## How busy the machine was over a 300 ms window of a resource-usage profile

- Question: machine CPU use while a 200 ms pointer move waited for its first animation frame.
- Command: `profiler-cli zoom push 249.5,254.5` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Got: one row per `CPU Use` marker with only its name, a start rounded to the second (`t=4m13s`) and its duration; the CPU percent is not shown. `marker info m-107..m-113` shows it, but one 20-line record per 100 ms sample.
- Workaround: `--json` and a script printing `start` and `fields.cpuPercent`.
- Could show: the marker's key payload field (CPU Percent) in the list row, and sub-second start times when the zoom is only a few seconds wide.

## `profile info` on a resource-usage profile says "No significant activity"

- Command: `profiler-cli profile info --session duration.py-1` on https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/co9NpsxFRD2SLWWwUEKlew/runs/0/artifacts/public/test_info/profile_resource-usage.json
- Expected: some sign that the profile's CPU data is in `CPU Use` markers.
- Got: "t-0: [tid 0] - 0.000ms", "CPU activity over time: No significant activity.", and `counter list` says "No counters in this profile", while there are 13,490 `CPU Use` markers with up to 89% machine CPU.
- Workaround: `thread markers` to find the `CPU Use` markers.
