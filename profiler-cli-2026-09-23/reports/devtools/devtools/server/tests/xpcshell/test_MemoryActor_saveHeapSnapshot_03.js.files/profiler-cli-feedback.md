## Question: "the exact start time of a marker"

- Command: `profiler-cli thread markers --search saveHeapSnapshot_03 --list --limit 0 --session ...` and `profiler-cli marker info m-1 --session ...`
- Expected: a start time precise enough to order events a few ms apart (test start at 132.972 s, `will retry` 33 ms later, first launch failure 1.3 s earlier).
- Got: `t=2m13s` in the list and `Time: 2m13s - 2m58s (45.029s)` in `marker info`, whole seconds only.
- Workaround: `--json` and read `start` (ms).
