## Question: which child processes were alive at time t (process lifetimes in seconds)?
- Command: `profiler-cli profile info --all --session <s>`
- Expected: each process's start and end as times (e.g. `32.179s → 34.357s`), to find the inference process that served a request at t=33.2s among 46 short-lived ones.
- Got: `[ts-5 → ts-a]` timestamp handles, which cannot be compared by eye. Workaround: a Python script over `profile info --all --json` reading `startTime`/`endTime`.
- Could show: seconds next to the ts handles, or a `--alive-at <t>` filter.

## Two processes with the same (reused) pid are merged into one process handle (review-browser_ml_native.js.bug1987878)
- Command: `profiler-cli counter list --session review-bug1987878-4` then `counter info c-91` on the Y1I0 profile (Windows reuses pids across the 44 short-lived inference processes).
- Expected: one process handle per process, so that the `Process CPU` counter of the process whose main thread is t-101 (pid 7528, 23.26s-24.34s) says `Main thread: t-101`.
- Got: both pid-7528 processes are listed as `p-26 Inference (25/44), pid 7528`, with two `Process CPU` counters (c-52 with 0 samples, c-91 with 64), and `counter info c-91` says `Main thread: t-65`, the other pid-7528 process.
- Workaround: matched the counter's time span against the `Process CPU Time` markers of t-101. Could show: a process handle per process start/end, or the counter's own main thread.
