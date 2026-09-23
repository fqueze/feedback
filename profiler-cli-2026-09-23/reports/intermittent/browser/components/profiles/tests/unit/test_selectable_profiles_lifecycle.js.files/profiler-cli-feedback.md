## Question: which of two markers in the same millisecond came first, and by how much?

- Command: `profiler-cli thread markers --session <s> --search "name:Runnable,-name:DummyEvent" --list --limit 0` (zoomed to a 20ms window after `zoom push 1.37,1.39`)
- Expected: start times precise enough to order markers. After t >= 1s, the list prints `t=1.519s` for every marker in that millisecond. In this case an `AsyncExecuteStatements::notifyCompleteOnCallingThread` at 1519.368ms and a `ChromeUtils::IdleDispatch` at 1519.486ms, and which one ran first decides the diagnosis.
- Got: `t=1.519s` for both, in list order. The list is chronological, but the gap between them and their exact times were not shown. Durations are printed in μs, but start times are not.
- Workaround: `--list --json` piped through python to print `flatMarkers[].start`, and `marker info <h> --json` per handle.
- What the default output could show: sub-ms start times (e.g. `t=1519.368ms`) when zoomed to a short range, or always the same precision as durations.
