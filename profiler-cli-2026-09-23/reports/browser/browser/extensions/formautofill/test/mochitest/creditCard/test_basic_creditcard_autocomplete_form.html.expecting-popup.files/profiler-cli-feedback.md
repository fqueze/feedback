## Resource-usage profile: `profile info` says "No significant activity" while the disk was saturated

- Question: "was the machine's disk or CPU saturated while the test waited?"
- Command: `profiler-cli profile info --session browser-cc-popup-2` on `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Xt9FcO2nRDy1D9CBrNy8dg/runs/0/artifacts/public/test_info/profile_resource-usage.json`, then `counter list`
- Expected: a summary of the machine's CPU and IO over time, since that is what this profile is for.
- Got: "CPU activity over time: No significant activity." and "No counters in this profile." The data is in 100 ms `CPU Use` / `IO` markers (iowait_pct, write_bytes), so it takes `thread markers --search "CPU Use,IO" --list --limit 0 --json` and a jq/awk script to see 169 MB/s of writes with 30-72% iowait for 12 s.
- Could show: `profile info` could summarize the `CPU Use` and `IO` markers the way it summarizes sample CPU, e.g. busiest windows by iowait and write rate.

## INFO markers print `(empty)` (same as the report next to this one)

- Command: `profiler-cli thread markers --session browser-cc-popup-1 --category Test --list --limit 0` on t-14 of the Xt9FcO2n per-test profile.
- Got: `[(empty)] INFO: (empty)` for every INFO line; the text is in `--json` `.flatMarkers[].data.message`.

## Review: `thread markers --search` does not find INFO messages in per-test profiles

- Question: "when did the test log `expecting a popup`?"
- Command: `profiler-cli thread markers --session browser-review-ccpopup-1 --search "expecting a popup,history_only_menu_checking" --list --limit 0` on t-14 of the Xt9FcO2n per-test profile (profiler-cli 0.9.0).
- Expected: the INFO markers whose `message` is that text (markerIndex 9456 and 9111).
- Got: no INFO marker, only the TEST-UNEXPECTED-FAIL. Their `message` field has `value` set but `formattedValue` "(empty)", so the search seems to match the formatted value. The same search finds the same INFO line in the job's resource-usage profile. Workaround: `--category Test --list --limit 0 --json` and filter `.data.message` with jq.
