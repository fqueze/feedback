## Average CPU over a zoomed range needs a script

- Question: "was the machine busy or idle during this 60 s wait?" on a resource-usage profile.
- Command: `profiler-cli zoom push 22.6,82.4 --session <s>` then `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: an aggregate (mean/max CPU Percent) of the CPU Use markers in view, or `profile info`'s "CPU activity over time" reflecting the CPU Use markers.
- Got: `profile info` says "No significant activity" (the resource profile has no samples, only CPU Use markers), and the marker list gives one row per 100 ms with no values; the CPU Percent is only in `marker info` or `--json`.
- Workaround: `--json` and a Python one-liner averaging `fields` of the flat markers.

## Min/max CPU over a linked range needs a script (review, same question again)

- Question: "does the report's 'CPU at X–Y% over that window' match the linked range?" on a resource-usage profile.
- Command: `profiler-cli zoom push 141.9,143.8 --session review-fogdel-1` then `profiler-cli thread markers --session review-fogdel-1 --search "name:CPU Use" --list --limit 0 --json`
- Expected: min/mean/max of `cpuPercent` for the CPU Use markers in view.
- Got: one row per 100 ms without the value; the percentage is only in `--json` `fields`.
- Workaround: Python over the JSON. Found the report's "9–94%" was really 25–94%.
