## Question: "which full GCs and CCs ran in this range?"
- Command: `profiler-cli thread markers --search 'name:GCMajor,name:CC' --list --limit 0 --session <s>` (after `zoom push 87.99,185`)
- Expected: only the `GCMajor` and `CC` markers.
- Got: `name:CC` is a substring match, so every `CCGCScheduler::EnsureGCRunner` Runnable and many telemetry markers matched; the handful of real GC/CC markers were buried.
- Workaround: `--category 'GC / CC' --list --json` piped through python, keeping `name in ("GCMajor","CC")`. An exact-name match (e.g. `name:=CC`) or a per-name list under `--category` would have answered it.

## Question: "in which order did these markers, all in the same second, happen?"
- Command: `profiler-cli thread markers --search ... --list` past the first minute prints `t=1m28s`.
- Expected: millisecond times, since ordering events 1 ms apart across threads (EngineIdleTimeoutForTests, DestroyEngineProcess, "Dropping ... keep-alive") was the question.
- Got: times rounded to the second once t >= 1 min.
- Workaround: `marker info <m> --json` one by one and read `start`. A `--precise-times` flag, or keeping ms precision in --list, would have answered it.
