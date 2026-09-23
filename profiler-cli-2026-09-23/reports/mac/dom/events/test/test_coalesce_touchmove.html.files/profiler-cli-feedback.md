## Log-type test markers (INFO) print "(empty)" for their message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_coalesce_touchmove.html-1` (content main thread t-18 of a mochitest-plain per-test profile), and `profiler-cli marker info m-6726`.
- Expected: the INFO line text, e.g. `finish` / `run file_coalesce_touchmove_browserchild2.html`.
- Got: `[(empty)] INFO: (empty)` in the list and `Level: (empty)`, `Message: (empty)` in `marker info`; `--json` shows `"value": "finish"` with `"formattedValue": "(empty)"`, so the formatter drops the value.
- Workaround: `marker info <handles> --json` and read `fields[].value`.

## `--list` times lose all sub-second precision past the first minute

- Command: `profiler-cli thread markers --search "eventType:touchstart,eventType:touchend,..." --list --limit 0 --session test_coalesce_touchmove.html-1` (profile 5m47s long, events of interest 1-15 ms apart around t=345-347 s).
- Expected: `t=345.170s` or `t=5m45.170s`, as the first minute prints `t=16.392s`.
- Got: `t=5m45s` / `t=5m46s` on every row, so the order of a touchstart, a paint and a pref update 10 ms apart could not be read, nor could a `zoom push` range be picked from it.
- Workaround: `--json` and read `flatMarkers[].start` (ms).

## Question: how many events of kind B happen inside each interval delimited by marker A?

- Question: "for each touch gesture (touchstart to touchend), how many touchmoves did the parent send to APZ, how many did it forward to the content process, and how many DOM touchmoves did content dispatch?" — the per-gesture counts are what showed 8 touchmoves consumed by APZ in one gesture and 0 in its twin.
- Commands: `thread markers --search IPC --group-by field:messageType` inside one `zoom push` per gesture worked, but needed one zoom per gesture, with gesture bounds that `--list` could not give (previous entry). I ended up with a script over `thread markers --list --limit 0 --json` (`runs.py` in this directory).
- What could have answered it: a `--group-by` that splits on occurrences of a delimiting marker (e.g. `--split-at eventType:touchstart`), printing one aggregate row per interval.
