## Machine CPU % at the time of a failure (resource-usage profile)

- Question: "was the machine saturated during these 2 s?"
- Command: `profiler-cli thread markers --session <s> --search 'CPU Use' --list --limit 0` (after `zoom push 125,128.5`)
- Expected: each `CPU Use` row showing its CPU Percent / Idle % (the only payload that matters for this marker type).
- Got: only name, start and duration per row; the percentages need `marker info` per marker, or `--json` plus a script over `fields[]`.
- Workaround: `--json` and a Python one-liner printing `fields` per marker. A per-row payload summary for `CPU` markers (or a `counter`-like "CPU over time" for resource-usage profiles, whose `counter list` says "No counters") would have answered it directly.

## `marker info --json` for several handles loses the handle

- Command: `profiler-cli marker info m-2 m-1 --session <s> --json`
- Expected: each record carries its `handle`.
- Got: `handle` is null in each record, so records cannot be matched back to the handles asked for except by order.
