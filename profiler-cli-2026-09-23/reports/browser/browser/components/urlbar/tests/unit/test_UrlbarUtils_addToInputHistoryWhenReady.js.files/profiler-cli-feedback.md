## Resource-usage `CPU Use` / `IO` markers list without their values
- Question: "what was the machine's CPU, iowait and disk write rate while this test ran?"
- Command: `profiler-cli zoom push m-1 --session ...; profiler-cli thread markers --session ... --search "name:CPU Use" --list --limit 0`
- Expected: one row per 100 ms interval with CPU %, iowait %, idle % (and for `IO`, write bytes).
- Got: rows with only name, time and duration ("CPU Use t=1m36s 105ms"); the values need `marker info` per marker. Times are also rounded to whole seconds ("t=1m36s") for every row, so 50 rows are indistinguishable.
- Workaround: `--json` and a Python script over `flatMarkers[].fields`.
