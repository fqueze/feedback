## Question: when did each child process start and end, in seconds?

- Command: `profiler-cli profile info --all --session browser_ml_native.js-1`
- Expected: process lifetimes as times (e.g. `33.20s → 34.36s`), to find which Inference process was alive at a given moment.
- Got: `p-47: Inference (46/46) [pid 2488] [ts-jo → ts-K]` — timestamp handles only, so 46 Inference processes could not be placed on the timeline without one `thread info` per thread.
- Also: `thread info --thread t-106` (GeckoMain of that same process) says `Ended at: still alive at end of recording`, while the process range ends at `ts-K` and the process shut down at 34.35s (its own `content-child-shutdown` marker). The two disagree; the thread one is wrong for an exited child.
- Workaround: `thread info` per thread and read the "CPU activity over time" seconds.

## Question: which child processes did not answer the final GatherProfile?

- Command: `profiler-cli thread markers --search GatherProfile --list --limit 0 --session browser_ml_native.js-1`
- Expected: the IPC message type and the peer pid on each row.
- Got: rows reading only `IPCOut  t=1m16s  0s` — no message type, no otherPid, so Msg vs Reply per pid could not be paired.
- Workaround: `marker info m-693..m-697 m-708..m-712 --json` and a Python script over `fields[]` for `messageType` and `otherPid`.

## Question: what was the machine CPU use over a 1.5 s window of the resource-usage profile?

- Command: `profiler-cli thread markers --list --limit 0 --session browser_ml_native.js-ru4` (zoomed on the test's `test` marker)
- Expected: each `CPU Use` row to show its CPU percent, and times with sub-second precision.
- Got: `m-521  CPU Use  t=9m47s  110ms` — no percent on the row, and every time in the 20-minute profile printed as `9m47s`/`9m48s`, so the order of the ML console logs and CPU samples inside one second could not be read.
- Workaround: `thread markers --list --limit 0 --json` and a script printing `start` relative to the `createGenerationStream` console marker plus the `cpuPercent` field.
