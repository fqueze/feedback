## Question: "at what exact time did this marker start, relative to another one ms away?"

- Command: `profiler-cli thread markers --session <s> --search conditional_breakpoint-04 --list --limit 0`
- Expected: start times precise enough to order markers 1-40 ms apart (the test started 38 ms before its "will retry" line; 1.4 s after the first launch failure).
- Got: `t=2m13s` for both the test marker and its "will retry" INFO line; seconds resolution only.
- Workaround: `--json` and a Python one-liner printing `start`.
- What would have answered it: millisecond starts in `--list` (e.g. `t=133.099s`), at least when the zoom or profile is short, or a `--precise` flag.

## Question: "how many markers of this kind started before time X, and how many in the burst after it?"

- Command: `profiler-cli thread markers --session <s> --search TIMEOUT --list --limit 0 --json` piped to a script counting `test` markers with TIMEOUT before/after the first launch failure, with min/max start and duration.
- Expected: a summary view of a filtered marker set: count, first/last start, duration range.
- Got: the aggregated (non-`--list`) view gives duration stats per name but not the start-time span, and cannot split at a timestamp without two zooms.
- Workaround: script over `--json`.
- What would have answered it: start-time min/max in the aggregated `thread markers` output.
