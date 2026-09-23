## Precise gap between two markers (test_bookmark_engine.js)
- Question: how long did the main thread take between `EventQueueInternal::PutEvent` (merge dispatched to the storage thread) and the next `TaskController::AddTask` of the same Rust function?
- Command: `profiler-cli thread markers --session test_bookmark_engine.js-1 --search '-name:Preference Read,-name:TaskController::AddTask' --list --limit 0` (zoomed to 1.074,1.145)
- Expected: start times with enough precision to read a 2 ms gap and to order markers within one millisecond.
- Got: `t=1.127s`, `t=1.130s`, `t=1.132s` for markers 0.1-2 ms apart; several distinct steps all print `t=1.132s`.
- Workaround: `--list --json` and a script printing `start` in ms with 3 decimals. The default list could print sub-ms precision once the zoom range is under ~1 s.

## Was the machine saturated in this window (resource-usage profile)
- Command: `profiler-cli profile info` / `counter list` on profile_resource-usage.json
- Expected: the machine CPU over time, as for a Gecko profile's CPU track.
- Got: "No counters in this profile" and "CPU activity over time: No significant activity." — the CPU is in 21,631 `CPU Use` markers.
- Workaround: `thread markers --search 'name:CPU Use' --list --limit 0 --json` in a zoom, then a script averaging `cpuPercent`. A per-bucket summary of `CPU Use` (as `counter info` gives for counters) would answer it directly.

## Default session directory not writable in the sandbox
- Command: `profiler-cli load <url> --session test_bookmark_engine.js-1`
- Got: EPERM on `mkdir ~/.profiler-cli`; the error message suggested `PROFILER_CLI_SESSION_DIR`, which worked. One wasted call; noting only because every agent in this fleet will hit it.

## `--json` view range in a different time base from marker starts (review-test_bookmark_engine.js)
- Question: which markers fall inside the range the report linked (range=1126000u9000), in ms?
- Command: `profiler-cli load '<profiler link with range=1126000u9000>' --session review-test_bookmark_engine.js-1`, then `profiler-cli thread markers --session review-test_bookmark_engine.js-1 --list --limit 0 --json`
- Expected: `context.currentViewRange` and `flatMarkers[].start` on one time base, so the view is 1126–1135 and the markers inside it start at 1126–1135.
- Got: `currentViewRange` is `{start: 1156.43, end: 1165.43}` and `rootRange.start` is 30.43, but the listed markers start at 1126.3–1134.4, and the plain output prints the one at 1127.386 as `t=1.127s`. So the context is offset by `rootRange.start` and the marker starts are not. I subtracted `rootRange.start` from the marker starts, got every time 30 ms off, and only caught it against the plain output.
- Workaround: ignore `context` and use `flatMarkers[].start` as is. `context` should use the same base as the markers, or name its base.
