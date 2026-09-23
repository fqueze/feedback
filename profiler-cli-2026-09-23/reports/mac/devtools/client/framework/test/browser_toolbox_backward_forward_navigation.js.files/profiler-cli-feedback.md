## Question: how many milliseconds between two markers in a `--list`?

- Command: `profiler-cli thread markers --search 'Leaving test,Assertion failure' --list --session …` on a resource-usage profile (task Sjnicww9SPajZ0R9Bi-VUQ).
- Expected: timestamps precise enough to order and space events that are milliseconds apart.
- Got: `t=2m10s` for both "Leaving test testSingleBackAndForthInstantNavigation" and the `Assertion failure` output line; the 16 ms gap between them, which was the key observation, is invisible. `marker info` also prints `Time: 2m10s (instant)`.
- Workaround: `--list --json` piped to a Python script printing `start/1000` with three decimals.
- What would have answered it: a millisecond-resolution time column in `--list` (at least when the view is under a few minutes, or with a `--precise-time` flag), and in `marker info`.

## Question: which "DOM Worker" thread runs which worker script?

- Command: `profiler-cli thread list --session …` on a per-test profile (task QWROLFQiSG2HsnQDvzeO8w) with 35 parent-process `DOM Worker` threads.
- Expected: a way to find the thread of `resource://devtools/client/shared/source-map-loader/worker.js`.
- Got: every row named `DOM Worker`, no script URL. `profile info --search source-map` matches nothing thread-level.
- Workaround: loop over every DOM Worker thread with `thread select` + `thread samples --limit 3`, grepping the `WorkerThreadPrimaryRunnable::Run <url>` frame label (36 calls); threads with no samples stay unidentified.
- What would have answered it: the worker script URL (from the `WorkerThreadPrimaryRunnable::Run` label frame) shown next to DOM Worker threads in `thread list` / `profile info`, or searchable with `--search`.
