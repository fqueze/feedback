## Marker times lose sub-second precision in long profiles

- Command: `profiler-cli thread markers --session <s> --list --limit 0 --search 'ContentTaskUtils,TEST-UNEXPECTED,notify[UrlbarProviderPlaces,badge-history'` on a 13-minute per-test profile (task Sb3UeB6_R0GlH_2muwmiCQ, profile_browser_aiwindow_smartbar_suggestions.js.json).
- Expected: `t=817.390s`-style times, as in short profiles, so that events a few ms apart can be ordered (the question was "did the wait's poll happen before or after the Places results arrived?", 1 ms apart).
- Got: every row printed as `t=13m37s`; `marker info` also prints `Time: 13m37s - 13m37s (112.90ms)`.
- Workaround: `--json` and reading `start`/`end` from `flatMarkers`. The question was "in which order did these markers happen", which the default output could answer by keeping milliseconds (e.g. `13m37.390s`).

## (review) Which images did each paint draw?

- Question: "was this DOM row rendered at the paint just before the test's poll?" — answered by the set of `Image Paint` names between consecutive `WrDisplayList` markers.
- Command: `profiler-cli thread markers --session <s> --search 'name:Image Paint,name:WrDisplayList' --list --limit 0 --json`, then a script grouping `Image Paint` names under the preceding `WrDisplayList`.
- Default output: one row per image (188 rows in 270 ms), with no grouping by paint. A per-paint summary (paint time, then image names with counts) would have answered it directly.

## (review) What code dirtied style at each point in a window?

- Question: "which DOM changes happened between 30.26 s and 30.34 s, and from which code?"
- Command: `thread markers --search name:SetNeedStyleFlush --list --limit 0 --json` for the handles, then `marker stack <h>` in a shell loop, one call per marker.
- The list could take an option printing the top N stack frames under each listed marker. Also, the loaded-from-raw-URL session selected `t-18 (GeckoMain, Privileged Content)`, not the parent main thread that the profiler link opens, so the first query ran on the wrong thread with no warning.
