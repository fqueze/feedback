# profiler-cli feedback — test_be_conservative.js

## Question: was the machine saturated while this one test ran?

- Command: `profiler-cli zoom push m-1 --session S` (the `test` marker in a resource-usage profile), then `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0`
- Expected: the machine's CPU over the zoomed range, e.g. mean / min / max of `CPU Percent`, or the percent on each listed row.
- Got: 305 rows of `CPU Use  t=10m55s  101ms` with no value; the percent is only in `marker info` or `--json`.
- Workaround: `--json | jq '.flatMarkers[] | .fields[] | select(.key=="cpuPercent")'` and awk for the mean. What would have answered it: a numeric-field summary (mean/min/max per payload field) in the aggregate `thread markers` view for a zoomed range, or the main field in the `--list` row.

## Question: when, within a range, did the samples containing function X happen?

- Command: `profiler-cli thread samples --include-idle --search GleanTimingDistribution --json`
- Expected: the times of the matching samples (it was 1 sample; I needed to know whether it was early or late in a 30 s stall), and whether the non-waiting samples under `testGetValue` were spread over the stall or bunched.
- Got: counts and call trees only; no sample times in text or JSON.
- Workaround: a shell loop of `zoom push a,b` / `thread samples --search X --json | jq .categoryBreakdown.totalSamples` / `zoom pop` over ten 3 s windows (40 calls). What would have answered it: a per-time-bucket count for the matching samples, like `counter info`'s "over time" section, e.g. `thread samples --search X --over-time`.

## Question (review): what did the test itself log, in its per-test profile?

- Command: `profiler-cli thread markers --category Test --search test_be_conservative --list --limit 0 --session S`, as `subagent-brief.md` step 3 recommends.
- Expected: the test's own log, including the `info()` lines from the test and its head files.
- Got: only `load_file` and `| Starting`. The per-test profile's INFO markers do not carry the test path, although the same lines in the resource-usage profile do (` — netwerk/test/unit/...`). The report's links, built with `profile-link.py --search test_be_conservative`, therefore selected markers that their own search hid.
- Workaround: `--category Test` with no search (the list is short in a per-test profile), then a search that matches the quoted text, such as `test_be_conservative,Snapshotting`.
- What would have answered it: a way to select the test-log markers of one test in a per-test profile. Also, `profile-link.py --marker m-N --search X` could refuse, or warn, when m-N does not match X.
