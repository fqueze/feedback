## Log markers (INFO) print "(empty)" for Level and Message in text output

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` and `profiler-cli marker info m-132 --session <s>` (content-process GeckoMain of a mochitest per-test profile, Linux ASan, task YKOMRNbtRCeIrBOMmUExCw)
- Expected: the INFO line's message, e.g. `### start testPlay, token=gizmo.mp4-0`.
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in marker info. The `--json` output has `value: "### start testPlay, token=gizmo.mp4-0"` but `formattedValue: "(empty)"`.
- Workaround: `--json` and read `fields[].value`. The question the default output failed to answer: "what did the test log (INFO) just before it hung".

## Question: "which runnable was running when this instant marker fired?" (ordering within one millisecond)

- Command: `profiler-cli thread markers --list --limit 0 --session <s>` after `zoom push 8.8505,8.8525`.
- Expected: timestamps precise enough to order markers inside the same millisecond, and to see that an instant marker (`HttpChannelChild::Cancel` at 8851.22 ms) lies inside an interval marker (`ListenerBatch::DispatchTask`, 8851.14–8851.30 ms).
- Got: every row printed as `t=8.851s`. The list order does not follow start time within a ms, and interval end times are not shown, so containment cannot be read off.
- Workaround: `marker info m-A m-B ... --json` and read `start`/`end`. What would have answered it: sub-ms times when the zoom is under ~10 ms, or an "enclosing interval markers" line in `marker info` for instant markers.
