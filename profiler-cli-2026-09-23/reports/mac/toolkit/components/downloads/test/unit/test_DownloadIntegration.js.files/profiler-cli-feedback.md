## Question: what was the whole-machine CPU during one test's run?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push 286.203,334.163` (a resource-usage profile, the range of one `test` marker).
- Expected: a summary of the `CPU Percent` field over the zoomed range (min / median / max, how many samples below e.g. 95%), since that is the only thing one reads 480 `CPU Use` markers for.
- Got: 480 rows, one per 100 ms sample; the aggregate view gives only marker counts and durations, not the field values.
- Workaround: `--json` and a Python script over `flatMarkers[].fields[key=cpuPercent]`.
- Could show: `--group-by` or a `--stats field:cpuPercent` option summarizing a numeric field of the listed markers.

## Question: which tests ran at the same time as this one, and how did they end?

- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0` on a resource-usage profile (665 `test` markers).
- Expected: a way to list only the markers overlapping a given marker's interval (e.g. `--overlapping m-1`), with the `status` field shown.
- Got: all 665 in job order; `zoom push` to the test's range keeps only markers starting in the range and drops the ones that started earlier and are still running.
- Workaround: `--json` and a script filtering on start/duration and printing `fields.name`/`fields.status`.

## Environment: default session directory not writable in the sandbox

- Command: `profiler-cli load <taskcluster URL> --session test_DownloadIntegration.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the error message itself suggested `PROFILER_CLI_SESSION_DIR`, which worked. One wasted call; already noted in STATUS.

Correction to the second entry above: `zoom push` does keep the `test` markers that started before the range and overlap it (the `parallel` marker from t=24 s is listed). The remaining ask is only a way to see the status field and filter by name/status without a script, e.g. `--group-by field:status` on the zoomed list of `test` markers (665 of them overlapped this 48 s range).

## Question: do events in two processes' profiles from the same job line up in wall-clock time?

- Context: per-test profiles of test_DownloadIntegration.js and test_DownloadCore.js from one job (SdmbN3WTTzauNi8_lVuQWQ), two sessions. The question was whether their `DownloadPlatform::DownloadDoneResolve` markers come at the same moments.
- Commands: `profiler-cli profile meta --session <s> --json` (for `startTime`) in each session, then `thread markers --search "NotifySaveComplete,DownloadDoneResolve" --list --limit 0 --json` in each, and a Python script to shift one onto the other's clock.
- Expected: some way to print marker times as absolute (wall-clock) times, e.g. `--absolute-times`, or a `profile meta` line giving the offset to use; then two plain `--list` outputs could be compared by eye.
- Got: times relative to each profile's own start only; `profile meta` text shows `Started:` to the millisecond, which is enough but needs manual arithmetic for every marker.
