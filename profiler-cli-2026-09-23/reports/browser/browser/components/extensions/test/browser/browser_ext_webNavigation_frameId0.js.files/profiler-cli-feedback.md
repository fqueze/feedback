## Question: which window's refresh driver kept ticking, from when to when

- Commands: `profiler-cli thread markers --session S --thread t-27 --search "innerWindowID:8589934634"` (0 matches), then `profiler-cli thread markers --session S --thread t-27 --search "RefreshDriverTick waiting for paint" --list --limit 0 --json` piped to a script to split the times by window.
- Expected: `--search innerWindowID:<id>` to match `RefreshDriverTick waiting for paint` markers, whose payload has `innerWindowID` (shown by `marker info` as "Other payload fields (no schema)", and usable with `--group-by field:innerWindowID`); or the JSON `fields` to carry it.
- Got: the search matched nothing, and the flat JSON markers had no `innerWindowID`, so the script could not attribute times to windows.
- Workaround: `--group-by field:innerWindowID` for the counts, then `zoom push` around the leaker's failure and `--list` to read when the ticks started. What would have answered it directly: `--group-by field:X` printing first/last time per group, or schema-less payload fields being searchable.

## Review note (profiler-cli 0.9.0): the flat JSON does carry `innerWindowID`

- Command: `profiler-cli thread markers --session S --thread t-27 --search "RefreshDriverTick waiting for paint" --list --limit 0 --json`
- Got: each `flatMarkers[]` entry has `data.innerWindowID`, so a script could split the ticks by window (first/last time, count in a range). `--search innerWindowID:<id>` still matched nothing.
- Question it was for: "from when to when, and how many in this range, did window X's refresh driver tick". `--group-by field:innerWindowID` showing first/last time per group would answer that without a script.
