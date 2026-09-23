## Question: "was the machine saturated while this test ran" (resource-usage profile)

- Command: `profiler-cli zoom push m-1` (the `test` marker) then
  `profiler-cli thread markers --search 'name:CPU Use' --group-by field:cpu_percent`
- Expected: the machine's average / p90 CPU use over the zoomed range.
- Got: `(no value): 1640 markers` (the key is `cpuPercent`; my guess was wrong, but even the right key only groups
  by distinct percentage strings). `profile info` says "No counters in this profile" and
  "CPU activity: No significant activity" for this profile, although its 45,196 `CPU Use` markers hold the
  machine's CPU. I had to script over `--list --json` and `data.cpuPercent` to get mean 33% / p90 59% over the test.
- What would answer it: `profile info` (or `thread info`) on a resource-usage profile summarising the `CPU Use`
  markers over the current view — mean, p90, time above 90% — as it does for sampled CPU.

## (review) `load` reports an error but the session loads anyway

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/.../profile_browser_smartwindow_manage_tabs_tool.js.json' --session review-mtt2057483-7`
- Expected: to wait for the load, or to say it is still loading in the background.
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`, then `Error: Profile still loading, try again shortly` on the next call. A few seconds later the session was fully usable.
- Workaround: `PROFILER_CLI_LOAD_TIMEOUT_MS=300000`.

## (review) Question: "how did the parent's JS heap grow over the test"

- Command: `profiler-cli marker info <GCMajor handles> --json`
- Got: `rawFields[0].value` for `timings` is cut at about 200 characters (`"truncated": true`), so only `total_time`, `reason`, `zones_collected`, `total_zones`, and `total_compartments` are there. The heap sizes after the truncation point are lost. The text output shows the same cut `timings: {...…}`. I had to script over the JSON to get the zone counts per GC.
- What would answer it: GCMajor `timings` parsed into fields, or untruncated in `--json`, plus a way to list one field per marker over a range, e.g. `--columns total_zones,total_time`.
