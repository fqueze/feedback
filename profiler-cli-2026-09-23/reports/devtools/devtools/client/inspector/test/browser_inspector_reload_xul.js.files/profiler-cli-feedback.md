# profiler-cli feedback (browser_inspector_reload_xul.js)

## Question: "at what exact time did each of these markers happen?" (long profiles)
- Command: `profiler-cli thread markers --category Test --search browser_inspector_reload_xul --list --limit 0 --session S` (profile is 7m56s long)
- Expected: marker times precise enough to order events a few ms apart (e.g. `t=470.931s` or `7m50.931s`).
- Got: `t=7m50s`, `t=7m51s` — whole seconds only, once the profile is over a minute long. `marker info m-46` also prints `Time: 7m51s`. Every ordering question (last DidComposite vs ForwardDPTransaction vs first "waiting for paint", 468.948 / 468.961 / 469.108) needed `--json` and a python script to read `start`.
- Workaround: `--json | python3 -c ...` printing `start/1000` with 3 decimals, for every list I read (about 15 times).
- Also: `--list` prints no duration for instant markers and no end time for intervals; `marker info` in text mode shows neither start nor end with sub-second precision.

## Question: "when did this process start and end?"
- Command: `profiler-cli profile info --session S`
- Got: `p-3: https://example.com [pid 5064] [ts-z → ts-zf]` — timestamp handles, not times.
- Workaround: `profile info --all --json` and reading `startTime`/`endTime`.

## Question: "what does the timeline of one marker kind look like, run-length encoded?"
- Wanted to see "RefreshDriverTick waiting for paint" as runs (from, to, count) between other markers; `--list` gives 975 rows. Had to script it over `--json`. A `--group-consecutive` or per-second histogram (`thread markers --search X --histogram 1s`) would have answered it.
