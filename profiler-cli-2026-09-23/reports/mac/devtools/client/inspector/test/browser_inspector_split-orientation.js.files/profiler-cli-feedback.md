# profiler-cli feedback (browser_inspector_split-orientation.js)

## Question: in what order did these events happen, within a few ms?
- Command: `profiler-cli thread markers --category Test --search split-orientation --list --limit 0` (and the same with `zoom push 245.72,245.95` and `--search DOMEvent`)
- Expected: times precise enough to order markers that are 0.1-10 ms apart, at least once zoomed into a 230 ms range.
- Got: every row printed `t=4m6s`; 30 test-log lines and ~170 DOMEvent markers spread over 130 ms had the same timestamp.
- Workaround: `--json | jq '.flatMarkers[] | .start'`. The list could print ms (or µs) once the view is shorter than a few seconds, or relative to the zoom start.

## Question: when was this setTimeout scheduled, and how late did it run?
- Command: `profiler-cli marker info m-238` (a `setTimeout callback` marker)
- Expected: the time its stack was captured (the `setTimeout()` call) next to the time it ran.
- Got: `Time: 4m6s - 4m6s (75.917μs)` and `Captured at: 4m6s`: both rounded to the second, so the 31 ms delay that was the whole diagnosis was invisible.
- Workaround: `marker info --json | jq '.stack.capturedAt, .start'`. Printing the capture time in ms, and the gap to the marker start ("scheduled 31.4 ms before it ran"), would answer it directly.

## Environment
- `profiler-cli load` failed with `EPERM ... mkdir '/Users/florian/.profiler-cli'` in the sandbox; used `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` (already known in STATUS.md).

## (review) Question: what did the test log say? First query after `load` timed out
- Command: `profiler-cli thread markers --category Test --search split-orientation --list --limit 0 --json --session review-split-1`, run straight after `load` had printed `Session started`.
- Expected: the marker list, or a message saying the daemon is still indexing.
- Got: `Error: Timed out after 30000ms waiting for the daemon on .../review-split-1.sock to answer.` The same command worked when retried.
- Workaround: retry. Either `load` should return only once the daemon can answer, or the error should say the daemon is still busy and a retry will work.
