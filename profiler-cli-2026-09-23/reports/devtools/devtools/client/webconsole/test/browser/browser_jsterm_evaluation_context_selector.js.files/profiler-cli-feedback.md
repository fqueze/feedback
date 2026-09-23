## Question: which of two markers on different threads came first, when they are less than 1 ms apart?

- Command: `profiler-cli marker info m-366 m-367 --session <s>` (and m-45, m-46 on another thread)
- Expected: start/end times with enough precision to order events that are 0.1-0.5 ms apart (worker thread start at 3187.109 ms against a main-thread dispatch at 3187.652 ms).
- Got: `Time: 3.188s - 3.189s`, rounded to the millisecond, so both read as t=3.187s/3.188s in `--list` as well.
- Workaround: `marker info --json` piped through a script to read `start`/`end`. Printing microsecond precision in `marker info` (or `--list` when the zoom is under ~50 ms) would have answered it.

## (review) Question: did the thread sleep between two markers 10 µs apart, and in which order did they happen?

- Command: `profiler-cli thread markers --list --limit 0 --search "name:Awake,name:EventQueueInternal::PutEvent,name:WorkerPrivate::DispatchLockHeld" --session <s>` inside a 1.5 ms zoom
- Expected: start and end times precise enough to see that an `Awake` interval ends, a gap follows, and then the next dispatch comes 10 µs after the thread wakes.
- Got: every row read `t=3.187s` / `t=3.188s`, and `Awake` rows show only a duration, so the sleep gap was invisible. Same root cause as the entry above, and it also affects `--list`.
- Workaround: piped `--json` `flatMarkers[].start` through a script, plus `marker info --json` for the `end` of each `Awake`. Showing µs in `--list` once the zoom is under ~50 ms would have answered it.
