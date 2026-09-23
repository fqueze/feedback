## `--search "name:CRASH"` also matched `test` markers whose test path contains "crash"

- Command: `profiler-cli thread markers --search "name:CRASH" --list --limit 0 --json` on an xpcshell resource-usage profile.
- Expected: only the `CRASH` markers.
- Got: also `test` markers such as `PASS — toolkit/crashreporter/test/unit/test_crash_phc.js`, so a job with 0 crashes showed 5. The guide's caveat ("name" is also a payload key) covers it, but the result looks plausible and is wrong.
- Workaround: filtered the JSON on the `signature` field.

## Question: "what was the average CPU, and the share of time saturated, over this range?"

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` (24 MB), then a script.
- Expected: something like `counter info` for resource-usage profiles, whose CPU data is `CPU Use` interval markers rather than a counter: time-weighted mean of `cpuPercent` and % of time above a threshold, respecting `zoom push`.
- Workaround: time-weighted average in Python over the flat markers.
