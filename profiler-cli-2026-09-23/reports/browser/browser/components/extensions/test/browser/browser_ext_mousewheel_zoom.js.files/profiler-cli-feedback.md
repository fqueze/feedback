## Marker times past one minute lose sub-second precision

- Question: at what time exactly did the test enter, start its end-of-test checks, and fail (to compare with the extension process's refresh driver ticks)?
- Command: `profiler-cli thread markers --category Test --search browser_ext_mousewheel_zoom.js --list --limit 0 --session ...` and `profiler-cli marker info m-6 m-47 m-16 --session ...`
- Expected: `t=110.020s`, `t=110.947s`, `t=115.976s` (ms precision, as below one minute: `t=2.787s`).
- Got: `t=1m50s`, `t=1m51s`, `t=1m56s` in both the list and `marker info` ("Time: 1m50s (instant)").
- Workaround: `marker info ... --json` and read `start`.

## Which document a refresh driver keeps ticking for

- Question: which document do the WebExtensions process's `RefreshDriverTick waiting for paint` markers belong to, and is it the same one for all 6380 of them?
- Command: `profiler-cli thread markers --search "innerWindowID:8589934601" --session ...` (to find the other markers of that window, e.g. its `DocumentLoad`)
- Expected: the markers carrying that innerWindowID (the payload key shown by `marker info` under "raw payload").
- Got: `No markers match the specified filters.` The raw (schema-less) `innerWindowID` is not searchable. `marker info` does show it, and the `DocumentLoad` of the same window also carries it only in `rawFields`.
- Workaround: `thread markers --search "waiting for paint" --list --limit 0 --json` and count `data.innerWindowID` in a script; then `marker info --json` on candidate `DocumentLoad` markers to match it by hand. What could have shown it: a `--group-by field:innerWindowID` that works on raw fields, or the window's URL next to the innerWindowID (the profile's pages table has it).
