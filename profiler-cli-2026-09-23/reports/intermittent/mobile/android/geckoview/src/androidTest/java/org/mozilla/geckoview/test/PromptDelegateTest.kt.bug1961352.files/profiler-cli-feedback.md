## Question: "what was the machine's CPU use, second by second, during this test?" (resource-usage profile)

- Command: `profiler-cli zoom push m-64 --session <s>; profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>`
- Expected: one row per `CPU Use` marker with its CPU / user / system percentages, or a compact per-interval CPU summary for the zoomed range.
- Got: rows with only name, time and duration (`m-1523 CPU Use t=25m39s 99ms`), no value; the handles are not in time order either (m-1518 sits between m-1523 and m-1524), so `marker info m-A..m-B` over a handle range is not a time range.
- Workaround: `thread markers ... --list --limit 0 --json` and a Python script over `flatMarkers[].fields` to print `cpuPercent`/`user_pct` sorted by `start`.
- What would have answered it: the CPU payload fields in the `--list` row (as for other typed markers), or `counter`-style "over time" output for the resource-usage CPU markers.

## Same question, hit again in review (2026-09-22)

- Command: `profiler-cli zoom push 1014.0,1028.0 --session <s>; profiler-cli thread markers --search "CPU Use" --list --limit 0 --session <s>`
- Got: the same value-less rows; needed `--json` and a script over `flatMarkers[].fields` again to get total/user % over the stall.
