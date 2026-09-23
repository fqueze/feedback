## Question: "in what order did these runnables run, within one millisecond"

- Command: `profiler-cli thread markers --list` / `marker info m-1083 m-1089 m-1090 m-102`
- Expected: start times precise enough to order markers that are microseconds apart (WorkerFetchResponseRunnable, ReleaseRefControlRunnable, ContinueConsumeBodyRunnable, Perform microtasks, all in 12.531–12.533 s).
- Got: `t=12.532s` for all of them. The text output rounds to 1 ms, and `marker info` shows `12.532s - 12.532s`.
- Workaround: `marker info --json` plus a script that prints `start`. Showing the time to 0.01 ms, when several markers in the list share the same rounded ms, would answer this without JSON.

## Question: "which marker does this link's `marker=N` point to" (review, 2026-09-22)

- Command: loaded the resource-usage link, then `thread markers --search retry --list`, then `marker info m-332 --json`.
- Expected: a way to open marker index N (from a profiler link) directly, e.g. `marker info --index 50361`.
- Got: the `retry` marker is index 50360, not the linked 50361, and nothing maps an index back to a handle.
- Workaround: `zoom push` around the time, then `marker info --json` on every handle in view, to find that 50361 is the `Retrying tests that failed during initial run` INFO marker. About 40 calls.
