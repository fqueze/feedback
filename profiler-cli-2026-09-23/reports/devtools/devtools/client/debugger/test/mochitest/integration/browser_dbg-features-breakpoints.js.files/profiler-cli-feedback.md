# profiler-cli feedback (browser_dbg-features-breakpoints.js)

## `thread markers --list` prints second-resolution times on profiles longer than a minute

- Command: `profiler-cli thread markers --session browser_dbg-features-breakpoints.js-1 --category Test --search browser_dbg-features-breakpoints --list --limit 0`
- Expected: marker start times precise enough to order events (ms), e.g. `t=366.609s`.
- Got: `t=6m1s`, `t=6m7s` for every row; 300 markers share six distinct timestamps, so the test log could not be lined up with anything else.
- Workaround: `--json` and a python script reading `flatMarkers[].start`.

## Question: "what happened on threads A and B between t1 and t2, interleaved, at ms precision?"

- Needed to show that a content main thread returned from `new Worker()` before the worker thread bound its debugger endpoint, and that the parent never received `PRemoteWorkerDebuggerManager::Msg_Register` for that worker.
- Commands: `thread markers --thread t-29 --search "-name:Preference Read" --list --limit 0 --json` after `zoom push 366.2,367.5`, then the same for t-30, t-31 and t-0, merged and sorted in a script.
- What the output could have shown: a `--list` across several `--thread` handles, sorted by start, with ms times and durations. The per-thread text list, even zoomed, had the same second-resolution times as above.

## `thread info` "Created at" is later than the thread's first markers

- Command: `profiler-cli thread info --session browser_dbg-features-breakpoints.js-1 --thread t-30 --json`
- Expected: `createdAt` at or before the thread's first marker.
- Got: `createdAt: 366418.24` while the thread's first markers (`Awake`, `CompileScriptRunnable`, `ScriptLoadRequestList::AppendElement`) are at 366411.76–366414.55. Same for t-31 (created 366476.65, first marker 366470.2, `Msg_RegisterDone` 366475.3). I could not use it as the thread start.
- Workaround: took the first marker as the thread's start.
