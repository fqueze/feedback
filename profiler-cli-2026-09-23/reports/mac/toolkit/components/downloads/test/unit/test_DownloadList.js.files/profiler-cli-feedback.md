## Question: "what was the machine CPU over this test's time window?" (resource-usage profile)

- Command: `profiler-cli zoom push 294.7,325.0 --session <s>; profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: the CPU percent per marker in the list, or a summary (mean/min/max of `cpuPercent` over the zoomed range), as `counter info` gives for counters.
- Got: a list of `CPU Use` rows with only time and duration; the percentage is only in `marker info` one marker at a time, or in `--json` `fields`.
- Workaround: `--json` and a Python script bucketing `data.cpuPercent` over the window. The resource-usage profile has no counters (`counter list` says "No counters"), so `counter info` cannot answer it either. Showing the CPU Use markers' payload as a counter, or a one-line summary for `--search "name:CPU Use"`, would have answered it.

## Question: "how long after each A did the next B come?" (each `BackgroundFileSaver::NotifySaveComplete` → its `DownloadPlatform::DownloadDoneResolve`)

- Command: `profiler-cli thread markers --session <s> --search "NotifySaveComplete,DownloadDoneResolve,Starting test_" --list --limit 0`
- Expected: a way to see the gap between consecutive matching markers, or between each A and the next B, since "what was it waiting for, and how long" is the core of every timeout diagnosis.
- Got: the chronological list with absolute times only; I subtracted by hand for the first profile (11 pairs), then scripted it.
- Workaround: `--json` and a Python loop over `flatMarkers` computing `start - previous A`. A `--gaps` / "time since previous row" column in `--list` output would have answered it directly.
