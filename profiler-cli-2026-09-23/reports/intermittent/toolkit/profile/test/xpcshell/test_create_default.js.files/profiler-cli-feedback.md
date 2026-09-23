## When was a given sample taken?

Question: at what time did the one sample in `nsIToolkitProfileService.selectStartupProfile` happen?

- Command: `profiler-cli thread samples-top-down --include-idle` inside `zoom push 1.040,1.082`
- Expected: some way to list sample times for a function or stack (e.g. `thread samples --search X --list` giving one row per sample with its timestamp).
- Got: only aggregated percentages, with no times.
- Workaround: bisected by hand with repeated `zoom push`/`zoom pop` over narrower ranges (1.040-1.045, 1.045-1.0475, …) until the sample was isolated. Took 3 rounds.

## Precise marker times

Question: the sub-millisecond start time of a few markers, needed to compare against epoch-ms values in a failure message.

- Command: `thread markers --list` shows `t=1.043s` (ms-rounded at >1 s). `marker info m-10` shows `Time: 1.043s - 1.082s`.
- Expected: at least 0.1 ms precision in `marker info`'s text output.
- Workaround: `marker info m-10 m-11 m-17 --json` plus a python one-liner to print `.start`.
- Also useful: `marker info` could print the absolute epoch time (`startTime + start`), since failure messages often carry `Date.now()` values.

## Which timebase is `startTime` in? (review)

Question: does `startTime + t` (t as shown by `thread markers` / `marker info`) give the epoch time of a marker?

- Command: `profiler-cli profile meta --json`
- Expected: one timebase throughout, or a field that says what `startTime` is the zero of.
- Got: `startTime` 1789836932122.442 and `context.rootRange.start` 144.93. Marker `start` values count from `rootRange.start`, not from 0. The artifact's `meta.startTime` is 1789836931977.516: profiler-cli added `profilingStartTime` to it without saying so, while `rootRange` stays in the artifact's units.
- Workaround: downloaded the raw artifact and compared a marker's raw `startTime` with profiler-cli's `start`. `startTime + t` is right, but only the raw file shows it.
