## `thread samples --include-idle` gives no idle count

- Command: `profiler-cli thread samples --include-idle --session <id>` after `zoom push 29.038,29.138` on a parent main thread. The profile has a 10 ms interval, and the range holds a 25 ms runnable.
- Expected: the idle and running sample counts for the 100 ms range, to tell whether the thread was idle while a timer was overdue.
- Got: `Categories (3 running samples)` with no idle count, so I could not tell if idle samples were included or missing.
- Workaround: read the `Runnable` markers with `--min-duration` and look at the gaps between them.
