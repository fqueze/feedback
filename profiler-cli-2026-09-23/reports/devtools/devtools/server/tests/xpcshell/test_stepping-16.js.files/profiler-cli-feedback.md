## Marker times past 1 minute lose their milliseconds (test_stepping-16.js, 2026-09-22)

- Question: when exactly did this test's `test` marker start, against the first launch failure, in a 3m37s resource-usage profile?
- Command: `profiler-cli thread markers --session test_stepping-16.js-2 --search test_stepping-16 --list --limit 0`, then `profiler-cli marker info m-1 m-3 m-7`
- Expected: a start time to the millisecond, as below 1 minute (`t=46.912s`).
- Got: `t=2m14s` in the list and `Time: 2m14s` in `marker info`, i.e. rounded to the second. The 1.86 s gap between the break and the test start was not readable.
- Workaround: `marker info ... --json` and read `start` (133532.908 ms).
