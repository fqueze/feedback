## Resource-usage `CPU Use` markers: list shows no CPU value

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` (after `zoom push` to the test's range)
- Question: "how busy was the machine while this test ran?"
- Expected: the `CPU Percent` per row, or a summary (mean/max over the zoom).
- Got: rows with only time and duration; needed `--json` plus a script to bucket `data.cpuPercent`.

## Resource-usage `C++ warning` markers: list does not show which test they belong to

- Command: `profiler-cli thread markers --session <s> --search "may be hanging at shutdown" --list --limit 0`
- Question: "which tests hit this warning in this job, and when?"
- Expected: the marker's `Test` field in the list row (as INFO/PASS rows already append the test path).
- Got: message only; needed `marker info` per handle or `--json` + script to read `Test`.
