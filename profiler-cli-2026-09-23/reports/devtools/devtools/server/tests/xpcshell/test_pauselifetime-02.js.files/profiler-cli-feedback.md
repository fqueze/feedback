## Were the replay's Begin, ERROR and End lines adjacent, in the same millisecond?

- Command: `profiler-cli thread markers --session <s> --search test_pauselifetime-02 --list --limit 0`
- Expected: times precise enough to order markers after t=60s, or the marker index shown.
- Got: `t=1m38s` for all three; ms precision and adjacency needed `marker info m-4 m-5 m-6 --json` (`start`, `markerIndex`).
- Also: in `thread markers --list --json`, `flatMarkers[].end` was `null` for the 45 s `test` interval marker (m-1); `marker info --json` had the right `end`.
- Workaround: `marker info --json` on the handles.
