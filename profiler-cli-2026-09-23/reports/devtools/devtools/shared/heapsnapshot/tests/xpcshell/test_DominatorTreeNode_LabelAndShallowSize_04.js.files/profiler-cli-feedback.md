## Question: how long after each test's start did its "will retry" line come?

- Command: `profiler-cli thread markers --search "name:test,will retry,not killing" --list --limit 0 --json --session <id>`, then a Python script to join `test` markers with `will retry` INFO markers by test path.
- The default output could not answer this: there are 1,153 `test` markers, and the pairing is by a path embedded in two different marker kinds.
- What would have shown it: nothing generic in profiler-cli. This is more a harness-log question (see fx-tests-feedback.md). Logged here because it cost a script.

## Question (review): which marker is at markerIndex N?

- Command: checking a report's `marker=10932` link. `thread markers --search "not killing" --list` gives handles only; I had to `zoom push` around the time, then run `marker info m-N --json` on each candidate until one reported `markerIndex` 10932 (6 calls).
- Expected: a way to go from a link's marker index to the marker, e.g. `marker info --index 10932`, or `markerIndex` in the `thread markers --json` `flatMarkers` entries.
