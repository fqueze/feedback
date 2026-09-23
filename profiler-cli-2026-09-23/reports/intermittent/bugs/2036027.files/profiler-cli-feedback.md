## Question: how many ms between the test's last log line and the assertion?

- Command: `profiler-cli thread markers --session 2036027-1 --search "Leaving test testSingleBackAndForthInstantNavigation,Assertion failure: ref,AddWorkerRef(this" --list --limit 0`
- Expected: start times precise enough to order and space events a few ms apart.
- Got: `t=1m59s` for all three markers (15 ms apart in reality); in an 18-minute resource-usage profile the `t=` column is rounded to the second.
- Workaround: `profiler-cli marker info m-448 m-450 --json` and read `start` (118809.252 vs 118824.252). A `t=` column with ms (or relative to the first row) would have answered it.

## Question: was the machine busy in the seconds before the crash?

- Command: `profiler-cli thread markers --session 2036027-1 --search "name:CPU Use" --list --limit 0` (after `zoom push 116,119`) on a resource-usage profile.
- Expected: the CPU percentage on each row.
- Got: only name, time and duration per row; `counter list` says "No counters in this profile".
- Workaround: `marker info m-950..m-960 --json` and extract `cpuPercent`. Showing the `cpuPercent` field in the row label would have answered it.

## C++ warning marker flagged as having a stack, stack is empty

- Command: `profiler-cli marker stack m-449 --session 2036027-1` (a `C++ warning` marker in a resource-usage profile, listed with the has-stack check mark).
- Expected: a stack, since the list marks it with a check mark.
- Got: `[1] unknown!null`.
- Workaround: none; ignored. The check mark should not be shown when the only frame is unknown.

## Question (review-2036027): which listed marker is the link's `marker=N`?

- Command: `profiler-cli thread markers --session review-2036027-1 --list --limit 0` (after `zoom push 116.5,119.5`), to check the report's links `marker=7839,7864,7885,7915,7945,7976,7977,7978`.
- Expected: the marker index on each row, or a way to look a marker up by index.
- Got: only handles (`m-164` ...); the index is only in `marker info <m> --json` (`markerIndex`), one call per candidate row.
- Workaround: a shell loop over `marker info --json` piped to python for `markerIndex` and `start`. A `--show-index` column, or `marker info --index N`, would have answered it.
