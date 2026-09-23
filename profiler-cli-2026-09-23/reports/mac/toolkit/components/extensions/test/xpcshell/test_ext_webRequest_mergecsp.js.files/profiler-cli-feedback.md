## Question: "was the machine CPU-saturated while this test ran?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push m-101` (the test's `test` marker).
- Expected: a summary of the `cpuPercent` field over the zoomed range (mean, min, share of time >= 95%), or a CPU track in `profile info` / `counter list`.
- Got: 719 individual 100 ms markers, each needing `marker info` to see its value; `profile info` says "CPU activity over time: No significant activity." and `counter list` says "No counters in this profile", although the machine was at 100% the whole time.
- Workaround: `--json` piped to a python script bucketing `data.cpuPercent` per 5 s.

## Review: `--search` does not match a Runnable marker's displayed label

- Command: `profiler-cli profile markers --search "PContent::Msg_SetProcessSandbox - priority" --json` (per-test profile of RImtaISARFC0EnQ1pRxOUg)
- Expected: the 50 `Runnable` rows whose label reads `PContent::Msg_SetProcessSandbox - priority: Normal (4) ...`.
- Got: `totalCount: 0`. The label is assembled from several fields, so only a substring of a single field matches.
- Workaround: `--search SetProcessSandbox`, then keep `name == "Runnable"` in a script.

## Review question: "which runnables fill this burst?"

- Command: `profiler-cli thread markers --search name:Runnable --group-by name,label --top-n 10` after `zoom push 5,6` (RImta parent main thread: 51,820 runnables in 1 s)
- Expected: counts per runnable name, e.g. `DummyEvent 51752`.
- Got: a single `(no value)` group of 1937 or more.
- Workaround: `--json`, then count `label` with its ` - priority...` suffix stripped.

## Review question: "was this thread awake but off-CPU, i.e. starved?"

- Command: `profiler-cli thread info` and `thread markers --search name:Awake --list` (TAOJNTmcQCiDti01vVH5MA parent main thread)
- Expected: in `thread info`, the total time covered by `Awake` markers next to the thread's CPU time (here 49 s awake against 11.3 s CPU over 62 s).
- Got: CPU time only. There are 993 `Awake` rows, each with its own `CPU Time` field.
- Workaround: `--json`, then merge the intervals and sum them in a script.
