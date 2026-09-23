## `thread markers --list` shows times at whole-second resolution in a long profile

- Command: `profiler-cli thread markers --session <s> --category Test --search frameId0 --list --limit 0`
- Expected: marker start times precise enough to order and space events (ms), as in short profiles.
- Got: in a 6m4s per-test profile, every row reads `t=5m56s` or `t=6m4s`; the 8 s vsync wait and the ms-apart TEST-PASS rows are indistinguishable. `marker info` also prints `Time: 5m56s`.
- Workaround: `--json` and read `flatMarkers[].start`.

## Question: which document's refresh driver keeps vsync on during a time range?

- Command: `profiler-cli thread markers --session <s> --search "waiting for paint" --list --limit 0 --json`, then a Python script counting `flatMarkers[].data.innerWindowID`, and a second search (`--search popup.html` in a zoom) to map the ID to a URL.
- Expected: the text output to group `RefreshDriverTick` markers by the document (innerWindowID, and its URL from the `initial timer start` marker) that owns them. I did not try `--group-by field:innerWindowID`; `--search innerWindowID:4294967338` returned 0 markers, since the ID is in raw fields only.
- Workaround: the script above.

## `--group-by field:innerWindowID` sees the raw field that `--search innerWindowID:` does not (review)

- Commands: `profiler-cli thread markers --session <s> --search "waiting for paint" --group-by "field:innerWindowID"` and `profiler-cli thread markers --session <s> --search "innerWindowID:4294967338" --list --limit 0`.
- Expected: both to accept the same field keys.
- Got: the group-by splits the 20,028 markers by window (20,025 / 1 / 1 / 1), which answers the question in the entry above without a script; the search matches 0 markers. So one flag knows `innerWindowID` and the other does not, and nothing says so.
- Workaround: group-by for counts; `--list --json` and a script for the times within one window.
