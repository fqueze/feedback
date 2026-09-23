## Ordering two markers that print the same millisecond

- Question: did `serviceWorkerRegistration.registration-changed` (front event) arrive before or after the reply of the last `contentProcessTarget:listWorkers()` request ended?
- Command: `profiler-cli marker info m-71 m-57 --session <id>` (and `thread markers --list`)
- Expected: enough precision to order them, or the end time of an interval marker printed next to its start.
- Got: `Time: 5.065s - 5.067s (2.179ms)` and `Time: 5.067s (instant)`, indistinguishable.
- Workaround: `marker info <m> --json` and read `start`/`end` (5066.906 vs 5066.980 ms). A `--precision` flag, or printing µs when two listed markers share a millisecond, would have answered it.
