## load of a 51 MB (gzipped) xpcshell per-test profile dies with SIGABRT

- Command: `PROFILER_CLI_SESSION_OWNER=test_http3_network_change.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/MdWUnK6pR9eJmtU2SWDgFw/runs/0/artifacts/public/test_info/profile_test_http3_network_change.js.json" --session test_http3_network_change.js-1`
- Expected: the profile loads (38 GB free on the machine).
- Got: first attempt, the command printed "Session ... is not reachable. The daemon exited without cleaning up." with a log containing only polling lines; second attempt, "The profiler-cli daemon died while loading the profile (killed by signal SIGABRT). Last lines of ...log:" followed by nothing. No hint that it was a V8 heap limit.
- Workaround: `NODE_OPTIONS=--max-old-space-size=8192` in front of the command; loads fine. The daemon could raise its own heap limit, or at least say "out of memory" when it aborts.

## Question: "how busy was the machine while this test ran?" (resource-usage profile)

- Command: `profiler-cli zoom push 675,750 --session <ru>` then `profiler-cli thread markers --session <ru> --search 'name:CPU Use' --list --limit 0 --json | python3 ...` to average `data.cpuPercent` per 5 s bucket.
- The resource-usage profile carries machine CPU as `CPU Use` markers, not as a counter, so `counter list` says "No counters in this profile" and `counter info` (which has the "over time" buckets) cannot be used. The default `thread markers --search 'name:CPU Use'` output only gives interval stats, not the CPU percent over time.
- Could have shown: a `counter info`-like "over time" summary of the CPU Use markers' cpuPercent for the current zoom (min/mean/max per bucket).
