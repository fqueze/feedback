# profiler-cli feedback

## `--search field:value` does not match a field of a schema-less payload

- Command: `profiler-cli thread markers --search "innerWindowID:4294967338" --list --limit 6 --session <s>` (also a bare `--search 4294967338`), on the WebExtensions main thread of the per-test profile of task QpSrFmkpR1Gt5mBA-g7xLw.
- Expected: the 1,553 `RefreshDriverTick waiting for paint` markers, whose `marker info` shows `innerWindowID: 4294967338` under "Fields (raw payload, no schema)".
- Got: `No markers match the specified filters.`, while `--group-by field:innerWindowID` on the same markers groups them by that value.
- Workaround: `--group-by field:innerWindowID`, then the example handles.
- The question behind it: "which document is this marker about". The innerWindowID is not resolved to a URL anywhere in the output; the `DocumentLoad` of the popup had to be matched by timing.

## Review: the innerWindowID of a `DocumentLoad` is only in `marker info --json`

- Command: `profiler-cli marker info m-157 --json --session <s>` (the popup's `DocumentLoad`, WebExtensions main thread, task QpSrFmkpR1Gt5mBA-g7xLw).
- Expected: the question "which document is this innerWindowID" answered by the default output, since `DocumentLoad` names the URL.
- Got: the id is in `rawFields` of the JSON only; the `thread markers --search DocumentLoad --list` rows do not show it. The report's author concluded the profile does not map the id to a URL, and matched by timing.
- Workaround: `marker info --json` on each candidate `DocumentLoad`, then `--group-by field:innerWindowID` on the ticks.

## Review: `--group-by innerWindowID` without `field:` groups everything under `(no value)`

- Command: `profiler-cli thread markers --search "RefreshDriverTick waiting for paint" --group-by innerWindowID --session <s>`
- Expected: groups by innerWindowID, or an error pointing at `field:innerWindowID`.
- Got: `(no value): 1556 markers`, silently.
- Workaround: `--group-by field:innerWindowID`.

## Review: `-field:value` exclusion on `profile markers` did not exclude

- Command: `profiler-cli profile markers --search "RefreshDriverTick,-innerWindowID:4294967338" --session <s>`, zoomed to 28.1–36.0 s.
- Expected: the RefreshDriverTick markers of any window other than the leaked popup, across threads.
- Got: the same 476 markers as without the exclusion, the excluded ones first (same root cause as the `field:value` entry above, presumably).
- Workaround: `thread select` each main thread and `--group-by name,field:innerWindowID`.

## Review: when did a periodic marker stop, per thread

- Question: "from when to when did this process receive vsync at 60 a second, and did the compositor stop observing earlier" (`PVsyncBridge::Msg_NotifyVsync` on the GPU Compositor vs `PVsync::Msg_Notify` on WebExtensions).
- Command: `thread markers --search <name> --list --limit 0` to a file, then a Python script bucketing the `t=` column per second.
- What would have answered it: a per-bucket count in the aggregate view (as `counter info` has "over time"), e.g. `thread markers --search X --over-time`.
