## Question: "at what precise time did each test log line happen?"

- Command: `profiler-cli thread markers --category Test --search split-orientation --list --limit 0` and `profiler-cli marker info m-23 m-35`
- Expected: timestamps precise enough to order events that are ms apart (e.g. `4m0.929s`).
- Got: `t=4m1s` for every marker between 4m0.80s and 4m0.94s in the list; `marker info` also prints `Time: 4m1s (instant)`. Twelve test steps within 140 ms all read the same.
- Workaround: `--json` and read `flatMarkers[].start`. The list could show ms precision once the profile is longer than a minute (e.g. `240929.3ms` or `4m0.929s`).

## Question: "how long after it was scheduled did this setTimeout callback run?"

- Command: `profiler-cli marker info m-273` (a `setTimeout callback` TextStack marker)
- Expected: the stack's capture time precise enough to subtract from the marker start (here 16.3 ms apart).
- Got: `Captured at: 4m1s` and `Time: 4m1s - 4m1s (51.417μs)`: both round to the same second.
- Workaround: `marker info --json` and read `stack.capturedAt` next to `start`. Printing the capture-to-start delay (e.g. "captured 16.3 ms before") would answer it directly.

