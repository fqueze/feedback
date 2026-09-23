## Question: which document's refresh driver ticked, and when?
- Command: `profiler-cli thread markers --session <s> --search RefreshDriverTick --list --limit 0`
- Expected: each tick's `innerWindowID` (which refresh driver / document it belongs to), since the parent main thread interleaves ticks for several chrome documents.
- Got: only the "Tick reasons" label; the `innerWindowID` is only in `--json` (`data.innerWindowID`), and nothing maps an innerWindowID to a document URL.
- Workaround: `--json` + a python loop printing handle/start/innerWindowID; matched the ID against the innerWindowID of a `SetNeedStyleFlush` whose stack touched the element of interest.
- What the output could show: the innerWindowID (ideally the document URL) as a column in `--list` for markers that carry one.

## Question: what dirtied style in this range, by stack?
- Command: `profiler-cli thread markers --session <s> --search SetNeedStyleFlush --list --limit 0` then `marker info m-a..m-b`
- Expected: a one-line top-of-stack per marker, to scan ~60 style invalidations for the one touching the sidebar.
- Got: the list has no stack column; `marker info` prints 20-frame stacks per marker (hundreds of lines).
- Workaround: `marker info <handles> --json` + a python script printing the first 6 non-native frames per marker.
- What the output could show: an option like `--list --stack-frames 5` with the top JS frames inline.

## Review (browser-review): both questions above came up again
- Command: `for i in $(seq 332 375); do profiler-cli marker stack m-$i --session <s>; done | grep launcherVisible` (ZD3y profile)
- Question: which `SetNeedStyleFlush` markers in this range have `set launcherVisible` in their stack, and which innerWindowID they carry.
- Got: no way to filter markers by a stack frame; 44 `marker stack` calls, plus `--json` for innerWindowID.
- What the output could show: a `--stack-contains <func>` filter on `thread markers`, and the innerWindowID column in `--list`.
