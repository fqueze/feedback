## `marker info` prints times rounded to the second
- Command: `PROFILER_CLI_SESSION_OWNER=2064988 profiler-cli marker info m-382 m-403 m-459 m-495 m-499 m-500 --session 2064988-1`
- Question: how many ms between "Leaving test ..." and the worker assertion, and how long did the second subtest's toolbox live?
- Expected: marker times with ms precision (e.g. `2m35.942s`), since `marker info` is the detail view.
- Got: `Time: 2m36s (instant)` for all of them; the list view is also rounded to the second.
- Workaround: `--json` and read `.start` (ms).

## Machine CPU % over a range in a resource-usage profile
- Question: was the machine saturated in the seconds before the crash?
- Command: `profiler-cli thread markers --session 2064988-1 --search "name:CPU Use" --list --limit 0` (after `zoom push 152,157`)
- Expected: each CPU Use row showing its `cpuPercent`, or a summary (min/avg/max CPU %) for the zoomed range; `profile info` says "No significant activity" and `counter list` "No counters", so the markers are the only source.
- Got: rows with name, time and duration only; the percentage is in the payload.
- Workaround: `--json` and a script over `.flatMarkers[].fields`.

## `thread markers` marks C++ warning markers as having a stack, but `marker stack` shows nothing (review-2064988)
- Command: `PROFILER_CLI_SESSION_OWNER=review-2064988 profiler-cli marker stack m-1 --session review-2064988-1` (m-1 = `C++ warning '!mWorkerPrivate->AddWorkerRef(this, aStatus)'` in the cBJqRzfaSz2FAs1UoYu4Ow resource-usage profile)
- Question: which call site's `StrongWorkerRef::Create` was refused?
- Expected: either a stack, or no ✓ in the `thread markers --list` "has stack trace" column.
- Got: the list shows ✓, but `marker stack` prints a single frame, `[1] unknown!null`. The same happens for every such marker in the UObj97 profile.
- Workaround: none. The crash stack from the minidump answered the question.
