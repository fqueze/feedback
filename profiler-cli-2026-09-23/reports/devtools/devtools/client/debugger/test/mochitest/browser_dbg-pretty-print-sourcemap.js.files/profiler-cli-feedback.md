## `zoom push` with millisecond values silently zooms past the end of the profile
- Command: `profiler-cli zoom push 257800,258400 --session <s>` (I had copied the millisecond `start` values from `--json` output)
- Expected: an error or a warning, since the profile is 4m20s long and 257800 s is far past its end.
- Got: `View: ts>10X→ts>10y (10m)` and then every query was empty (`No markers match the specified filters (searched 200 threads)`), with nothing saying why.
- Workaround: divided by 1000 (`zoom push 257.8,258.4`).

## Ordering markers within one second, once past t=1m
- Question: "in what order, to the millisecond, did these markers on two threads happen" (a worker `message` against main-thread `message` events and test log lines).
- Command: `profiler-cli thread markers --thread t-0 --category Test --search <test> --list --limit 0`
- Got: every row shows `t=4m18s`. The markers I needed were 5 to 100 ms apart, so the text output could not order them.
- Workaround: `--json` and a Python script printing `start` and `duration`. Printing milliseconds (e.g. `t=258.301s`) in `--list` would have answered it.

## When did a given JS function run
- Question: "at what time was the sample in `newSources.js!loadSourceMap/<`, relative to the worker's `message` at 258272.36 ms?"
- Command: repeated `zoom push a,b` + `thread samples-top-down --include-idle | rg ...`, splitting the range until each side held one sample.
- Could have shown: the sample timestamps (or the first and last sample time) for a function from `thread functions --search X` or `thread samples --search X`.

## Which thread posted this runnable, and when (review)
- Question: "when did the main thread post the message the worker received at 258272.36?" (the worker's `MessageEventRunnable runnable: 9dde12b83bce0dd90`).
- Command: `profiler-cli thread markers --thread t-145 --list` then `marker info m-10`: it shows the runnable id, but nothing points to the `EventQueueInternal::PutEvent (flow=9dde12b83bce0dd90)` on t-0 that posted it.
- Workaround: `--json` dumps of both threads for the window, and a Python grep for the id. (Did not try `profile markers --search <id>`.)
- Could have shown: for a Runnable or flow marker, the other markers with the same flow or runnable id on every thread, with their times: poster, then runner.
