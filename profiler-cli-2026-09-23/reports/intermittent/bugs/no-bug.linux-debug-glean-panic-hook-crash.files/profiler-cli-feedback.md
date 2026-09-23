# profiler-cli feedback (no-bug.linux-debug-glean-panic-hook-crash)

## Question: "machine CPU over a time window" in a resource-usage profile

- Command: `profiler-cli zoom push 54.1,56.3; profiler-cli thread markers --search "CPU Use" --list --limit 0`
- Expected: a mean, min and max of `cpuPercent` over the zoomed range, which is what "was the machine saturated when X happened" needs.
- Got: one row per 100 ms marker, with no aggregate of the payload field. `marker info` then shows one marker at a time.
- Workaround: `--json`, then a script (`cpustats.py`) averaging `data.cpuPercent`.

## `C++ warning` markers flagged as having a stack, with an empty stack

- Command: `profiler-cli marker stack m-78 --session ...-1` (resource-usage profile of Hr1wCyNaRtmK4aTSZ6YiJg, marker `NS_ENSURE_TRUE(inst) failed — StaticComponents.cpp:13,324`)
- Expected: a stack, since the list shows it with a check mark for "has stack trace".
- Got: `[1] unknown!null`. These markers come from the harness parsing test output, so there is no real stack. The check mark is misleading.

## (review) `marker info --json` changes shape with the number of handles

- Command: `profiler-cli marker info m-215 m-216 ... --json --session review-glean-panic-2` versus `profiler-cli marker info m-97 --json`
- Expected: the same shape either way, for example always a list of marker records.
- Got: one handle returns the record itself (`markerIndex`, `fields`, ...). Several handles, or an `a..b` range, return `{type, requested, markers, errors, context}`. My script failed twice before I noticed.
- Workaround: read `["markers"]` when there are several handles.
