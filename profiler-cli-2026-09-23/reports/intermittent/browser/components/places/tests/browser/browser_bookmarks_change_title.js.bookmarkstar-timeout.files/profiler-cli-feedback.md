## Question: in what order did these markers happen, to the millisecond?

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session bookmarkstar-timeout-1` (zoomed on 68.09,68.6)
- Expected: start times that order events a few ms apart past t=60 s.
- Got: `t=1m8s` on every row; the 500 ms window I zoomed on is one value.
- Workaround: `--json` piped through a small script printing `start` (ml.py in this directory).
- Could show: ms precision in `--list` when the view spans a few seconds, e.g. `1m8.5572s`.

## Question: which window did each of these markers belong to, and what caused it?

- Command: `profiler-cli thread markers --search 'name:SetNeedStyleFlush' --list --limit 0 --session bookmarkstar-timeout-1` (80 markers, two browser windows open)
- Expected: per row, the innerWindowID (or document URL) and the top JS frames of the marker's stack, so the style changes of the new window can be told from the old one's and read at a glance.
- Got: `SetNeedStyleFlush` and nothing else per row. It took one `marker stack` call per handle, or `marker info <h1> <h2> ... --json` with a script (stacks.py here) reading `rawFields.innerWindowID` and `stack.frames`.
- Could show: an `--show-stack N` / `--show-field innerWindowID` option on `--list`, or a `--window <innerWindowID>` filter.

## Question (review): what time range is my zoom actually showing?

- Command: `profiler-cli zoom push 68.48,68.56 --session review-browser_bookmarks_change_title.js.bookmarkstar-timeout-1`, then `profiler-cli marker info m-104 --json`
- Expected: `context.currentViewRange` in the same time base as `zoom push` and as each marker's `start`.
- Got: `currentViewRange.start` 68490.21 (= 68480 + `rootRange.start` 10.21), while the listed markers and their `start` values were in the 68480 base. It also matched the offset of the raw IPC payload `startTime` fields. So I read my view as 10 ms off and re-zoomed twice before I noticed.
- Could show: one time base everywhere, or a label on `currentViewRange` saying which base it uses.
