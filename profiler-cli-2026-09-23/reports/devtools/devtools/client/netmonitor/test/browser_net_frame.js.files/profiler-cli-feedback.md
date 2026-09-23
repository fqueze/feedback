## Question: in what order, to the millisecond, did these markers happen within one second?
- Command: `profiler-cli thread markers --session browser_net_frame.js-1 --category Test --search "Verifying,UNEXPECTED" --list --limit 0` (and `profile markers --search img_request`)
- Expected: a start time precise enough to order markers (e.g. `t=2m35.638s` or ms).
- Got: every row printed `t=2m36s`; the whole race played out inside that second, so the text list could not order a content-process notification against a network request.
- Workaround: `--json` piped to a Python script printing `start` in ms.
- What would have answered it: sub-second precision in `--list` time column when the profile is long (or a `--time-precision ms` flag).

## Units: `--json` marker `start` is in ms, `zoom push` takes seconds
- Command: `profiler-cli zoom push 155690,155704 --session browser_net_frame.js-1` using values copied from `marker info --json` `start`.
- Expected: an error or a warning for a range far outside the profile (profile is 157 s long), or ms accepted with a unit suffix.
- Got: silently pushed a range read as seconds (view "14s"), then `thread samples-top-down` returned an empty tree.
- Workaround: divide by 1000 (`zoom push 155.690,155.704`).

## Network marker raw payload times are not in profile time
- Command: `profiler-cli marker info m-650 --session browser_net_frame.js-1` (a `Load 9:` Network marker on a content thread)
- Expected: `startTime`, `requestStart`, `responseEnd` etc. comparable with the marker's own `Time:` and with other markers.
- Got: "Fields (raw payload, no schema)" with `startTime: 155676.305` while the marker's `start` (JSON) is 155669.068: a ~7.24 ms offset (process/profile start offset), so `requestStart` cannot be compared with other markers without computing the offset by hand.
- Workaround: offset = payload `startTime` - marker `start`, subtracted from each phase.
