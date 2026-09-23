## Marker times after the first minute are rounded to the second

- Command: `profiler-cli thread markers --search test_breakpoint-09 --list --limit 0 --session test_breakpoint-09.js-2` and `profiler-cli marker info m-1 m-3 m-7 --session ...`
- Question: at which millisecond did this test start, relative to the first launch failure (both at "2m12s"/"2m13s")?
- Expected: `t=133.029s` (or `2m13.029s`), as markers before 1 minute get (`t=46.385s`).
- Got: `t=2m13s` in both the list and `marker info` (`Time: 2m13s - 2m58s (45.035s)`), so two events 1.4 s apart, or 38 ms apart, cannot be ordered or spaced.
- Workaround: `marker info ... --json` and read `start`.
