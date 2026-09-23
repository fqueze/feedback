## How late did each setTimeout fire? (scheduling-to-firing latency of timer callbacks)

- Command: `profiler-cli thread markers --search "interval 0ms" --list --limit 0 --session <s>`, then `marker info <70 handles> --json` and a Python script comparing `stack.capturedAt` with `start`.
- Expected: `setTimeout callback` markers carry the stack captured when `setTimeout` was called, so the list (or `marker info`) could show the delay between scheduling and firing, which is the question for any "timer fired too late" race.
- Got: the list shows only the firing time; `marker info` shows "Captured at" one marker at a time. Answering "which 0 ms timers were 20+ ms late" needed the JSON and a script.
- Could have shown: a `scheduled at` / `latency` column for markers whose stack capture time differs from their start (TextStack `setTimeout callback`, `setIdleCallback handler (timed out)`).

## Was the machine busy at time T? (resource-usage profile)

- Command: `profiler-cli load .../profile_resource-usage.json`, `profile info` ("CPU activity over time: No significant activity."), `counter list` ("No counters in this profile."), then `thread markers --search "CPU Use" --list` and a script over `marker info --json` to read `cpuPercent` / `idle_pct`.
- Expected: `profile info` or a counter to show machine CPU over time for the resource-usage profile, since that is what the profile is for.
- Got: "No significant activity" and no counters; the CPU series exists only as one `CPU Use` marker per 100 ms, whose list rows show no percentage.
- Could have shown: the `CPU Percent` value in the `--list` row of `CPU Use` markers, or a CPU-over-time summary in `profile info` built from them.

## Empty call tree with no explanation

- Command: `profiler-cli zoom push 23.589,23.604` then `thread samples-top-down --include-idle` (session on COBDLuadQ5iNSpC-MkMLGA, 10 ms sampling).
- Expected: "No samples in this range" or similar.
- Got: the `Top-Down Call Tree:` header followed by nothing.
- Workaround: widened the zoom until samples appeared.

## Did this setTimeout callback fire from an nsITimer or from an immediate dispatch? (review-browser_accessibility_panel_toolbar_checks.js)

- Command: `profiler-cli thread markers --search "MenuButton.js:287" --list`, then for each of the 14 markers a script that ran `zoom push <marker range>`, `thread markers --search SetNeedStyleFlush --list --json` and `marker info <handles> --json`, and looked for `nsTimerImpl::Fire` in the stack of the callback's own style change.
- Expected: a `setTimeout callback` marker to say how it was run, since its stack is the one captured at scheduling time, not at firing.
- Got: no firing-side information on the marker. The only way to tell was a marker with a stack captured *inside* the callback, which not every callback produces (5 of 14 had none).
- Could have shown: the enclosing `Runnable`/Task label for a marker (`TimeoutExecutor Runnable` via `nsTimerImpl::Fire` vs direct), or a samples/label stack at the marker's start, in `marker info`.
