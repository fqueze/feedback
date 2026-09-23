# profiler-cli feedback (test_protocol_index.js)

## Bare negative search terms silently match nothing

- Command: `profiler-cli thread markers --session <s> --search "-name:ERROR,-name:CPU Use,-not killing,-return code" --list --limit 0` (with a zoom active)
- Expected: every marker except those, or an error saying a bare `-term` is not supported.
- Got: `0 markers (filtered from 1674)` / "No markers match the specified filters." Dropping the bare `-term`s (keeping only `-field:value`) worked.
- Workaround: only exclude with `-field:value`, and filter the rest out with `rg -v`.

## Question: "was the machine saturated at time T?"

- Command: `profiler-cli thread markers --search "name:CPU Use" --list` on a resource-usage profile.
- The list shows each `CPU Use` marker with an empty description: the CPU %, user %, idle % are only in `--json`'s `fields`. I needed a script over `--json` to read them.
- Could have shown: the `cpuPercent` / `idle_pct` value in the list row, or a small over-time summary of machine CPU (like `counter info`'s buckets) for the current zoom.

## Question: "which tests were running at time T?"

- Command: `profiler-cli thread markers --search name:test --list --limit 0`, then a Python script to keep markers with start < T < start + duration.
- Needed this three times, once per resource-usage profile, to see which tests were in flight when process launch started failing.
- Could have shown: a `--at <t>` (or `--overlapping <t>`) filter on `thread markers` that keeps interval markers spanning that instant. `zoom push` keeps markers overlapping the range, so this is close to `zoom push t,t+0.001` but the long markers (`parallel`, `Phase`) and the sort make that awkward.
