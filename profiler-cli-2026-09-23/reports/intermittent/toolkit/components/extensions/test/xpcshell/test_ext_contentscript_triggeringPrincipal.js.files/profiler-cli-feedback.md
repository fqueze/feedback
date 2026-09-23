## Question: "how busy was the machine during this time window" (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>` after `zoom push 508,543`.
- Expected: some summary of the `CPU Use` markers' `cpuPercent` over the zoomed range (avg / min / max, or per-N-seconds buckets).
- Got: 352 rows of `CPU Use  t=8m28s  96ms`, with no value. The value is only in `marker info` or `--json`.
- Workaround: `--json` piped into a Python script averaging `data.cpuPercent` per 5 s bucket.
- What could have shown it: the `CPU Use` marker type's `cpuPercent` in the list row, or an aggregate for numeric fields when `--list` is not given (the By Name view already shows interval stats).

## `zoom push` with minute-style times silently zooms to the wrong range

- Command: `profiler-cli zoom push 8m28s,9m03s --session <s>`
- Expected: zoom to 508 s – 543 s (the list output prints times as `t=8m28s`, so that format seemed accepted), or an error.
- Got: `Zoom depth: 1`, and `status` showed a 1 s view (`ts-1 to ts-2`), with no warning.
- Workaround: `zoom push 508,543`.
