## `marker info` prints times at 1 s resolution

- Command: `profiler-cli marker info m-1 --session test_blackboxing-05.js-1` (resource-usage profile of LKys8j8iTQKTAN9hKv040g)
- Question: when did this test start, relative to the first launch failure 1.3 s earlier?
- Expected: the start and end to the millisecond, as `thread markers --list` shows for short times (`t=46.353s`).
- Got: `Time: 2m13s - 2m58s (45.033s)`. Once past one minute, both `marker info` and `--list` round to whole seconds, so two events 1.3 s apart cannot be ordered.
- Workaround: `marker info ... --json` and read `start`.

## `zoom push` on a harness "replaying full log" marker misses the lines it replays

- Command: `profiler-cli zoom push m-2` (the `replaying full log for …test_blackboxing-05.js` marker, 16 ms long), then `thread markers --list`
- Expected: the replayed log lines inside that zoom.
- Got: only long interval markers overlapping it. The replayed `INFO`/`ERROR` lines are at t=178047.908 ms, 47 ms after the marker's start (178000), which is outside it. The same thing happened in the other job.
- Workaround: `zoom push 178.04,178.06` around the `ERROR` marker's own time.

## `--search name:INFO` matched Test markers

- Command: `thread markers --list --search "name:INFO,name:ERROR" --json`
- Got: `test` markers only (their payload has a `name` key); no INFO lines. This fits the documented caveat, but it means you cannot select log lines by level.
