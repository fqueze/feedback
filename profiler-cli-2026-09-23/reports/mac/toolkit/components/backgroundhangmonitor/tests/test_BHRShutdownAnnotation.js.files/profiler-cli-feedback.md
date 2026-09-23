## Daemon dies silently loading a large (869 MB) per-test profile

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=test_BHRShutdownAnnotation.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/SgvtLGZKSZS0SCWu7Q7IoQ/runs/0/artifacts/public/test_info/profile_test_BHRShutdownAnnotation_retry.js.json" --session test_BHRShutdownAnnotation.js-2`
- Expected: the profile loads (the resource-usage profile's `artifact` marker gives its size as 869MB; the 477MB first-run profile of the same test loaded fine), or a clear error saying it is too large.
- Got: `Error: Session test_BHRShutdownAnnotation.js-2 is not reachable. ... The daemon exited without cleaning up.` The daemon log ends at `Fetching profile from ...` with no error line: no reason given (likely V8's maximum string length or heap, given the size). About 6 GB was free.
- Workaround: none within profiler-cli; I fell back to downloading the file and searching its raw JSON with `rg` for marker names, with no links possible.

## Question: how regularly did the sampler sample over a range (the largest gap between samples)?

- Asked because the sampler thread is the only non-main thread whose wakeup latency a CI profile shows, and the question was whether threads of the process were woken late (the BHR monitor thread's timer is ignored if it fires more than 64 ms late).
- Commands tried: `profiler-cli thread samples --include-idle` (gives a count, 37 samples in 1.036 s, but no timestamps), `profiler-cli counter info c-1 --json` (only bucketed values), `profiler-cli profile info` (CPU per range, no sample times).
- Needed: a script over the raw profile's `samples.timeDeltas` / `samples.data[time]` to compute gaps (mean 28 ms and max 50 ms for a 10 ms interval during the hang; 70 gaps over 64 ms afterwards).
- What would have answered it: sample count, mean/max interval and a histogram of inter-sample gaps in `thread info` or `thread samples` for the current zoom, next to the configured interval.

## Question: what was the machine's CPU use during one test (resource-usage profile)?

- Command: `profiler-cli zoom push m-1 --session ...-ru` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` lists 447 rows with no value column; `marker info` shows one at a time.
- Needed: `--json` and a script to average `data.cpuPercent` over the zoom (100% throughout the first run, about 25% during the retry).
- What would have answered it: the CPU percent in the `--list` row label for `CPU` markers, or a mean/min/max of the payload fields when a search matches only one marker type.
