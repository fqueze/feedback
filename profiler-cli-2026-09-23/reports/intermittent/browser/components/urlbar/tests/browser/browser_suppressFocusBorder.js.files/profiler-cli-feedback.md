# profiler-cli feedback (browser_suppressFocusBorder.js)

## Question: which browser window does each DOMEvent marker belong to?

- Command: `profiler-cli thread markers --search "innerWindowID:70" --list --limit 0 --session browser_suppressFocusBorder.js-1`
- Expected: the DOMEvent markers whose payload has `innerWindowID: 70` (`marker info` shows that field under "Other payload fields (no schema)"). The guide says `field:value` narrows to a payload key.
- Got: `0 markers`. The `--list` output doesn't show `innerWindowID` either, so every `MozAfterPaint - window` row looks the same even though several windows are painting.
- Workaround: `--search MozAfterPaint --list --limit 0 --json`, then a Python script that reads `flatMarkers[].data.innerWindowID`.
- What would have answered it: let `--search` match fields that have no schema (`innerWindowID`), or show the window id in list rows for markers that have one.

## Question: which state transition did each GC slice make?

- Command: `profiler-cli thread markers --search "GCSlice" --list` in a zoomed range.
- Expected: something like `Mark -> Sweep` for each slice, because that tells you when sweeping happened.
- Got: GCSlice rows with no description. `marker info m-354 --json` (GCMajor) returns `timings` truncated (`"truncated": true`) and cut off after `minor_gc_number`.
- Workaround: ran `marker info <each slice> --json` and parsed `rawFields[].value` (the JSON string) for `initial_state`/`final_state`.
- What would have answered it: put `initial_state -> final_state` (and the reason) in the GCSlice list label, and don't truncate raw payload fields in `--json`.
