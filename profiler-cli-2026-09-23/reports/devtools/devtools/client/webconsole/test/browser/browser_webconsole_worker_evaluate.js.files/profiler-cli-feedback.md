## `thread markers --list` rounds timestamps to whole seconds in long profiles

- Command: `profiler-cli thread markers --category Test --search browser_webconsole_worker_evaluate.js --list --limit 0 --session browser_webconsole_worker_evaluate.js-1` (profile of 2m53s)
- Expected: marker start times with at least ms precision, since the test's own log spans ~6 s and ordering against other markers matters.
- Got: `t=2m47s` for every INFO marker between 166.756 s and 167.263 s, and `t=2m53s` for everything from 172.68 s on; the text cannot order or time the test's steps.
- Workaround: `--json` and a script printing `start/1000` with 3 decimals.
- Question the default output did not answer: "at what time, to the ms, did each step of the test's log happen?"

## `marker info` and `profile markers` also print times rounded to whole seconds

- Commands: `profiler-cli marker info m-1251 --session browser_webconsole_worker_evaluate.js-1` printed `Time: 2m47s - 2m47s (611.00μs)`; `profiler-cli profile markers --search RemoteWorkerDebugger --session ...-1` printed `t=2m45s` for every row.
- Expected: ms (or better) start times. The question was "did the worker thread bind before the main thread's `new Worker()` script ended?", which needs sub-ms ordering between a ScriptExecution marker, a WorkerThreadPrimaryRunnable on another thread, and IPC markers on a third.
- Got: second-level times; the duration alone is precise.
- Workaround: `--json` and printing `start/1000` with 5 decimals, plus `threadHandle`/`pid` from `profile markers --json`.
- Question the default output did not answer: "in which order, to the ms, did these markers on different threads and processes happen?"

