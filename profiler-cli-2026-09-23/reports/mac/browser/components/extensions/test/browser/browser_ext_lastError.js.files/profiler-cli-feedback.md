## Question: "at what time, to the millisecond, did each line of the test's log happen?"

- Command: `profiler-cli thread markers --category Test --search lastError --list --limit 0 --session browser_ext_lastError.js-1`
- Expected: a time column precise enough to order and cite markers (e.g. `t=142.441s`), since a test's whole log here spans 26 ms and the report cites times to the ms.
- Got: `t=2m22s` for every row from `Entering test` to `Leaving test`, and `marker info` prints the same rounded `Time: 2m22s`. Once a profile is past 60 s, the list cannot say which of two markers came first or how long a gap was.
- Workaround: `--json` and read `flatMarkers[].start` (ms) in a Python one-liner, for every list I cited.
- What would have answered it: print seconds with 3 decimals (`t=142.441s`) in `--list` and `marker info`, at least when the view spans more than a minute.

## Question (review-browser_ext_lastError.js): "which document is each of these RefreshDriverTick markers for, and how many per document in this range?"

- Commands: `profiler-cli thread markers --thread t-20 --search RefreshDriverTick --group-by name,innerWindowID --session review-browser_ext_lastError.js-1` and `... --search 'innerWindowID:4294967338'`
- Expected: groups per innerWindowID (ideally with the window's URL), or a filter on that field. The report's claim is that all 508 ticks in the vsync wait belong to one leaked popup.
- Got: `--group-by` puts all 8,540 markers under "(no value)", and the field search matches 0 markers. `marker info m-2410` does show `innerWindowID: 4294967338` under "Fields (raw payload, no schema)", so the value exists but can't be searched or grouped on. `--list` rows don't show it either.
- Workaround: `--list --limit 0 --json` and a Python script grouping `flatMarkers[].data.innerWindowID`, then matching the ID to the `DocumentLoad` marker that has the same `innerWindowID`.
- What would have answered it: `innerWindowID` (and the other no-schema raw fields) usable in `--search field:value` and `--group-by`, with the URL of that window's `DocumentLoad` shown next to the ID.
