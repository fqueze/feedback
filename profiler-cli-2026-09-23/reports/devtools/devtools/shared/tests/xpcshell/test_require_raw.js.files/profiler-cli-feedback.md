## Question: "when did tests start failing in this job, and how many were running at once?"
- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0` (2642 rows)
- Expected: a way to see how test outcomes are spread over time, such as status counts per time bucket or concurrency.
- Got: a flat list too long to read. Answering took `--json` and a Python script that bucketed Test markers by start time and status.
- Could have shown: `--group-by field:status` combined with time buckets (a histogram over time for a grouped marker set).

## Bare-term exclusion in `thread markers --search` silently matches nothing
- Command: `profiler-cli thread markers --session <s> --search "-name:CPU Use,-name:Memory,-name:IO,-name:NetIO,-name:Sampling Interval,-name:test,-return code,-full log,-force-killed" --list` (with a zoom)
- Expected: those terms excluded, or an error saying that bare `-term` exclusion is not supported.
- Got: "0 markers (filtered from 4964)", with no warning. It looked as if nothing had happened in that window.
- Workaround: kept only the `-field:value` exclusions and filtered the rest with `rg -v`.

## `marker info --json` has no handle field
- Command: `profiler-cli marker info m-6 m-11 m-13 --session <s> --json`
- Expected: each record to carry its `handle`, so the records can be matched to the requested handles.
- Got: `handle` absent (None). The records only match up by their order.

## Question: "which handle is the marker this link points at?" (review-test_require_raw.js)
- Command: `profiler-cli load '<profiler.firefox.com link with marker=11657>' --session <s>`, then `thread markers --search "not killing" --list`
- Expected: `load` to print the handle of the linked `marker=N`, or a way such as `marker info --index 11657` to look it up.
- Got: `load` ignores `marker=`. The search returns 1057 identical "not killing -- proc or pid unknown" rows, so finding index 11657 meant running `marker info m-A..m-B --json` over handle ranges until one reported `markerIndex` 11657.
- Could have shown: the linked marker's handle, printed by `load`.
