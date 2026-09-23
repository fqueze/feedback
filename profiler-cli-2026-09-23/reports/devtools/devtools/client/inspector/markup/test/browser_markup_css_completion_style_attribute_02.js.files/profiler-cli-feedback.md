## Which window each timer callback belongs to, and when it was set

- Question: for the `setTimeout callback` markers in a range, which `innerWindowID` each ran in, and when its `setTimeout` was called. This is how you tell two windows' `TimeoutExecutor`s apart, and see how late a timer ran.
- Command: `profiler-cli thread markers --search "setTimeout callback" --list --limit 0 --session <s>` (and `--json`)
- Got: name and start time only. `innerWindowID` (under `rawFields`) and the stack's `capturedAt` appear only in `marker info`. The `--json` flatMarkers have neither.
- Workaround: pipe the handles from `--list --json` into `marker info <handles> --json` and script over it.
- Could show: `win=<id>` and `set at <t>` columns on `--list`, for any marker that has `innerWindowID` or a captured stack.

## A one-line stack summary in marker lists

- Question: which JS caller dirtied style at each `SetNeedStyleFlush` in a 40 ms window (show, hide or setItems).
- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session <s>`
- Got: one row per marker, marked ✓ for having a stack, but with no frames shown. That takes a `marker info` call per marker.
- Workaround: `marker info m-A..m-B --json`, then a script printing the first 7 non-native frames.
- Could show: an option such as `--stack-summary N` that prints the top N JS frames on each row.
