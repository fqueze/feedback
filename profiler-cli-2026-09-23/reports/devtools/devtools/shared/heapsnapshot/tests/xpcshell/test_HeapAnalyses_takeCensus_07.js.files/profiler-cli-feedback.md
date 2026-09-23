# profiler-cli feedback (test_HeapAnalyses_takeCensus_07.js)

## Times past one minute lose their milliseconds

- Command: `profiler-cli thread markers --search "not killing" --list` and `profiler-cli marker info m-1032 m-1041 m-10` (session loaded from the PyUxuOpdQj2b3T3XyQOKpg resource-usage profile)
- Expected: `t=98.215s`, as markers under 60 s print (`t=53.202s`)
- Got: `t=1m38s` for every marker between 98.0 and 98.9 s, so ten markers 2 ms apart are indistinguishable and can't be ordered against a test's end, including in `marker info` (`Time: 1m38s (instant)`)
- Workaround: `marker info ... --json` and read `start`

## Question: "was the machine saturated between t1 and t2?"

- Command: `profiler-cli counter list` on a resource-usage profile says "No counters in this profile"; the CPU is only in `CPU Use` markers (overlapping, several per interval)
- Needed a script over `thread markers --search "name:CPU Use" --list --limit 0 --json` to average cpuPercent per second over 44-100 s
- What could answer it: `counter list`/`counter info` exposing the resource-usage CPU as a counter, or a per-bucket summary for `CPU Use` markers in the zoomed range
