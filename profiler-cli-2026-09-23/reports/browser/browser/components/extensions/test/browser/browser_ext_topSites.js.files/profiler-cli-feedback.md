## Times past 60 s lose their milliseconds in text output

- Command: `profiler-cli thread markers --session browser-browser_ext_topSites.js-2 --search "name:TEST-,...,RankShortcuts" --list --limit 0`, then `profiler-cli marker info m-197 m-198 m-38 m-28 --session ...`
- Expected: `t=59.625s`-style times, as below 60 s.
- Got: `t=1m`, `t=1m1s`, and `Time: 1m (instant)` in `marker info`, for markers 330 ms apart. The race I was measuring (query at 59.625 s, refresh done at 59.955 s) was invisible.
- Workaround: `--json` and a script printing `start/1000`.

## Question: was the machine saturated (CPU, iowait, disk writes) while this test ran, compared with its retry?

- Command: `profiler-cli load <profile_resource-usage.json> --session ...-3`, then `profile info` ("CPU activity over time: No significant activity.") and `counter list` ("No counters in this profile.").
- The answer was there as `CPU Use` / `IO` markers every 100 ms (`iowait_pct`, `write_bytes`): iowait 25-50% and ~17 MB written per 100 ms during the failing test, ~1% during the retry.
- What would have answered it: `profile info` (or a zoomed `thread info`) summarizing the `CPU Use` / `IO` markers of a resource-usage profile over the view, e.g. mean/max cpu, iowait and write rate, instead of "No significant activity".
- Workaround: `thread markers --search "name:CPU Use,name:IO" --list --limit 0 --json` and a script bucketing by 5 s.

## A 25-marker `--list` produced 123 KB

- Command: `profiler-cli thread markers --category Test --search browser_ext_topSites.js --list --limit 0 --session ...-1`
- Expected: one line per marker.
- Got: 123 KB, because the `TEST-PASS` messages carry base64 data: URIs (favicons) printed in full. The harness spilled it to a file.
- Workaround: `cut -c1-260` on the saved file. A per-row width cap on the description (with a flag to lift it) would avoid this.

## Loading a per-test profile by its Taskcluster URL selects the WebExtensions main thread (review)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/UMUMF3KbQt-jm5ah-vZ4_w/runs/0/artifacts/public/test_info/profile_browser_ext_topSites.js.json --session browser-review-browser_ext_topSites.js-1`, then `thread markers --search ... --list`.
- Expected: the parent process GeckoMain (t-0) selected, as the test log and nearly all the evidence live there, and as loading the profiler.firefox.com link with `thread=0` does.
- Got: `t-48 (GeckoMain, WebExtensions)` selected; the marker search returned "No markers match", which reads like an absence rather than the wrong thread.
- Workaround: `thread select t-0`.
