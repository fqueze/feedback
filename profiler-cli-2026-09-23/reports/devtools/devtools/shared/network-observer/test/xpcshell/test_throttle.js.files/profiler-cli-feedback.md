## `zoom push` silently misparses the time format the tool itself prints
- Command: `profiler-cli zoom push 8m50s,9m15s --session test_throttle.js-1`
- Expected: a zoom to 530–555 s (the list output prints times as `t=9m11s`), or an error.
- Got: `Pushed view range: ts-1 (8s) to ts-2 (9s)`: parsed as 8 s–9 s with no warning. The next marker list covered the wrong second.
- Workaround: pass plain seconds (`530,555`).

## Question: "in what order did these instants happen within one second?"
- Command: `profiler-cli thread markers --list --search 'left-over,...'`
- Past one minute, `--list` rounds times to whole seconds (`t=9m11s`). I needed the order of a `ps` DEBUG line against the INFO line 1 ms after it, and of launches ~100 ms apart. I had to use `marker info ... --json` / `--list --json` and read `start`.
- Could have shown: millisecond precision (e.g. `t=9m10.637s`), at least when neighbouring rows share the same rounded second.

## Question: "how did the processes on the device change over time?" (harness `get_process_list` DEBUG payloads)
- Command: `profiler-cli thread markers --list --limit 0 --search get_process_list --json`, then a Python script that diffed the lists across snapshots (52 MB of JSON for 912 markers).
- The payloads are cut off in text mode, so neither "which snapshot lists process X" nor "which pid changed name" can be answered without a script. This is niche; a per-marker `--full` payload view with `--search` inside the payload would have covered the first half.
