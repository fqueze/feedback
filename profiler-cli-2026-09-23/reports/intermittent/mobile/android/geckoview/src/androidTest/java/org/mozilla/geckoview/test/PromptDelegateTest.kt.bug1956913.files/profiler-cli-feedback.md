## Question: "what was the machine's CPU use during this 8 s window"

- Command: `profiler-cli thread markers --session <s> --search 'CPU Use' --list --limit 0` after `zoom push m-1` (the failing `test` marker) on a resource-usage profile.
- Expected: the `cpuPercent` of each 100 ms marker, or a min/median/max summary over the zoomed range.
- Got: 95 rows of `CPU Use  t=20m19s  99ms` with no value. `counter list` says "No counters in this profile".
- Workaround: `--json` and a script over `flatMarkers[].fields` (`cpuPercent`), and the same over the whole profile for the job median (a 43 MB JSON).

## Same question, hit again by the reviewer ("what was the machine's CPU use during this test")

- Command: `profiler-cli thread markers --session <s> --search 'CPU Use' --list --limit 0`, on the report's link (range = the failing `test` marker).
- Got: the same 95 value-less rows; the same `--json` + `flatMarkers[].fields` workaround was needed to check the report's 13.6–40.8% figures.
