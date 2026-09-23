## Question: "machine CPU per 100 ms bucket during one test, in a resource-usage profile"

- Command: `profiler-cli zoom push m-1 --session <s>` (the `test` marker), then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>`
- Expected: each `CPU Use` bucket with its `cpuPercent`, in time order.
- Got: rows show only name, coarse `t=17m23s` and duration, and not the CPU value. The list is chronological but the handles are not (m-115, m-112, m-113, m-114, m-110, ...), so a handle range such as `m-110..m-124` is not a time range. `profile info` says "No significant activity" and `counter list` has no counters, although the profile has 16913 `CPU Use` markers.
- Workaround: `marker info m-1 m-110..m-124 --json` piped to a script, sorting by `start` and reading `fields[].cpuPercent`.
- What would have answered it: the marker's main field (or `--fields cpuPercent`) as a column in `--list`, with ms-precision times relative to the zoom start.
