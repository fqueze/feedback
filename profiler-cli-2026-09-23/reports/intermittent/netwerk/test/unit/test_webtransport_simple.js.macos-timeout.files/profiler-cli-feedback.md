## Daemon dies with SIGABRT loading a large CI xpcshell profile

- Command: `PROFILER_CLI_SESSION_OWNER=test_webtransport_simple.js.macos-timeout profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/KjdDTzeSSiGvg2u-Rj2HeA/runs/0/artifacts/public/test_info/profile_test_webtransport_simple.js.json" --session test_webtransport_simple.js.macos-timeout-3` (tried twice)
- Expected: the profile loads (44.7 MB gzipped; a 19.7 MB one with 3.5M markers and another with 7.5M markers loaded fine).
- Got: `Error: The profiler-cli daemon died while loading the profile (killed by signal SIGABRT).` The daemon log ends at `Fetching profile from ...` with no error line, so there is no hint of the cause (probably the Node heap limit; 43 GB were free). The first attempt printed only "is not reachable ... The daemon exited without cleaning up".
- Workaround: picked another task's profile. It would help if the daemon logged the reason (heap OOM) and the load said how to raise the limit, or streamed markers.

## Question: what was the machine's CPU use during one test? (resource-usage profile)

- Command: `profiler-cli zoom push m-1 --session ...-r1` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Expected: the CPU use over the zoomed range (avg/min/max, or per-second buckets), since the resource-usage profile carries it only as `CPU Use` marker payloads (`cpuPercent`), with no counter (`counter list` says "No counters in this profile").
- Got: a list of 357 identical-looking `CPU Use 100ms` rows with no values; the percentages are only in `marker info` one at a time or in `--json` `data.cpuPercent`.
- Workaround: a Python script over `--json` bucketing `data.cpuPercent` per 2 s. The default output could show the payload's key numeric field in the list row, or `thread markers --group-by` could aggregate a numeric field.

## Question: which tests ran at the same time as this one? (resource-usage profile)

- Command: `profiler-cli zoom push m-1` then `profiler-cli thread markers --search name:test --list --limit 0`
- Expected: the `test` markers overlapping the range with their test path and status.
- Got: rows labelled only `test` (the path and status are in the payload), so the list cannot answer it; the `--search test_webtransport_simple` form works only when you already know the name.
- Workaround: script over `--json` `fields` (`test`, `status`). Showing the marker's own label/description (`TIMEOUT — <path>`, which `marker info` and `--search` rows do show) in `--list` rows would answer it.
