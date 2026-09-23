# profiler-cli feedback — browser_ext_commands_onCommand.js

## Question: which document do these markers belong to?

- Command: `profiler-cli thread markers --session onCommand-1 --search "innerWindowID:4294967331"`
  and `--search "4294967331"`, on the WebExtensions main thread.
- Expected: the markers of that inner window (`marker info --json` shows `innerWindowID` in
  `rawFields`), and some way to map an innerWindowID to its page URL.
- Got: `0 markers`. `--group-by field:innerWindowID` does work, so the field is there, but it is
  not searchable, and nothing maps the id to a URL: I found the page only by matching the id against
  `marker info --json` of each `DocumentLoad` marker in a script.
- Workaround: `--group-by field:innerWindowID`, then `marker info --json` on DocumentLoad markers.
- What would have answered it: `--search innerWindowID:<id>` matching, and the page URL printed
  next to markers that carry an innerWindowID (the profile's `pages` table has it).

## Question: is this periodic marker stream continuous, and since when?

- Command: `profiler-cli thread markers --search "name:RefreshDriverTick waiting for paint" --list --limit 0 --json`
  piped to a script computing first/last time and gaps over 50 ms.
- Expected: for a stream of 2,241 instant markers, its first and last time and its largest gaps,
  without listing all of them.
- Got: the aggregate view gives count and intervals (min/avg/max) but not the first/last time or
  where the gaps are.

## Negative IPC durations in aggregate output (Windows profile)

- Command: `profiler-cli thread markers --session onCommand-2 --search "messageType:PVsync::Msg_Notify" --group-by field:niceDirection`
- Got: `sent to WebExtensions (Thread ID: 9140): 308 markers (avg=-496,516ns, max=-286,664ns)`.
  Cross-process clock skew presumably; a negative duration printed as-is reads as a bug in the data.
