## Question: when did this job start failing everything, and what was running at that moment?

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json` then a Python script bucketing test markers by start/end time and status.
- Expected: a way to see the test timeline (test markers with status, start and end) as counts over time, or the tests overlapping a given time.
- Got: 1,113 rows of `test` markers; answering "which tests were running at t=131.676" and "486 tests started within 2 s" needed a script over the JSON.
- Could have shown: `thread markers --search name:test --at <t>` (markers spanning a time), or `--group-by` over time buckets with the status prefix.

## Timestamps in `--list` are too coarse to order events within a second

- Command: `profiler-cli thread markers --session <s> --search "Failed to launch" --list`
- Expected: millisecond timestamps (t=131.676s) to order the first launch failure against the crashes and test starts around it.
- Got: `t=2m12s` for every marker in that second.
- Workaround: `--json` and `start` in ms.

## A bare `-term` exclusion in `--search` silently matches nothing

- Command: `profiler-cli thread markers --session <s> --search "-name:CPU Use,-name:Memory,-name:IO,-name:NetIO,-name:Sampling Interval,-name:test,-name:PASS,-CONSOLE_MESSAGE,-head.js" --list --limit 0`
- Expected: either the bare `-CONSOLE_MESSAGE` excluding markers containing that substring, or an error.
- Got: "0 markers ... No markers match the specified filters." with no hint why.
- Workaround: dropped the bare terms and filtered with `rg -v`.
