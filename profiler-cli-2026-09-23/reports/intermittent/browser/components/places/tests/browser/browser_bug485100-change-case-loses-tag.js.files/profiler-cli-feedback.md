## Question: in what order, to the millisecond, did markers within one second happen?

- Command: `profiler-cli thread markers --session S --category Test --list --limit 0` (also with `--search RefreshDriverTick,...`)
- Expected: start times precise enough to order events a few ms apart (e.g. `t=80.3536s`).
- Got: every row printed as `t=1m20s`, so a location change, a click, a flush and an error 5-50 ms apart all read as the same instant.
- Workaround: `--json` piped to a script printing `start` / `duration`.
- What would have answered it: print times with ms precision (at least when the zoom range is under a few seconds, or always as `80.354s`).

## Question: which window/document does this Layout / RefreshDriverTick marker belong to?

- Command: `profiler-cli thread markers --session S --list --search "innerWindowID:456"` → no results; the text list does not show innerWindowID either.
- Expected: filter or column by innerWindowID, since in a two-window test, which window's refresh driver ticked is the whole question.
- Got: nothing matched; the field is only in `--json` `data.innerWindowID` (it shows as a raw, no-schema field in `marker info`).
- Workaround: `--json` + script printing `data.innerWindowID`.
- What would have answered it: make `innerWindowID:N` searchable, and show `w=N` (or the window's URL) in list rows.

## Question: was the machine saturated when the failure happened? (resource-usage profile)

- Command: `profiler-cli profile info` / `counter list` / `thread samples` on `profile_resource-usage.json`
- Expected: a CPU track or summary for a time range.
- Got: "No significant activity", "No counters in this profile", 0 samples; the CPU data are only `CPU Use` markers whose `cpuPercent`/`idle_pct` are not in the text list.
- Workaround: `thread markers --search "name:CPU Use" --list --json` + script.
- What would have answered it: surface `CPU Use` markers as a counter (or print cpuPercent/idle_pct in the list rows).

## Question (review): at which ms did each DidComposite reach the main thread? (same precision issue, another command)

- Command: `profiler-cli profile markers --session S --search DidComposite` (zoomed to 200 ms)
- Expected: per-row start times in ms, to line DidComposite up with the RefreshDriverTick that follows.
- Got: every row `t=1m20s`, as with `thread markers --list` above.
- Workaround: `--json` + script printing `markers[].start`.
- What would have answered it: the same ms-precision fix, applied to `profile markers` too.
