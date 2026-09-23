## Which interval markers overlap a time

- Question: which tests were running (and in which harness slot) at t=896.478 s, when this test started?
- Command used: `profiler-cli thread markers --search name:test --list --limit 0 --json` plus `--search XpcshellTestRunnerService --list --limit 0 --json` (20 MB), then a Python script to keep the `test` intervals overlapping t and join each to its launch line.
- Would help: a `--overlapping <t>` (or `--at <t>`) filter on `thread markers` that keeps interval markers spanning that time. `zoom push` to a tiny range keeps them too, but mixed with every instant marker of the range.

## Reading a long log line in a list

- Question: which processes named `…test_runner:xpcshellN` were in each `get_process_list` DEBUG listing around the failure?
- Command: `profiler-cli thread markers --search get_process_list --list --limit 0` truncates each ~20 KB message to its first processes (init, kthreadd, ...), so the app processes at the end never show. `marker info` shows one in full, but there were 8 in the window and 1,368 in the job.
- Workaround: `--json` (77 MB) and a script parsing the Python list in each message.
- Would help: a way to show only the matching part of a long payload in `--list` (e.g. highlight/context around the `--search` term, or a `--grep` over payload text that prints the matching fragment).

## Which marker a link's `marker=N` points to (review-test_HeapAnalyses_takeCensusDiff_01.js)

- Question: for each of the 42 links in a report, is the marker at index N the one the report quotes?
- Command: `profiler-cli marker info --marker m-1..m-256 --json` (and two more ranges) over a `zoom push` window, then a script mapping `markerIndex` to handle, time and message. Markers outside the window needed their own `--search` first.
- Would help: `marker info --index N[,M...]` (or accepting the link itself), resolving a link's `marker=` param without a search.
- Also: `profiler-cli --session <id> thread markers ...` fails with `unknown option '--session'`; it is only accepted after the subcommand. A global position would save a retry.
