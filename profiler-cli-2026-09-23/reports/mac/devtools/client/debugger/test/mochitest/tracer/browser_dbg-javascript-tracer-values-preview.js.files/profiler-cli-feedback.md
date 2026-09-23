
## review-browser_dbg-javascript-tracer-values-preview.js (2026-09-22)

- **Two time bases in one JSON record.** `profiler-cli marker info m-530 m-63 m-10 --session <s> --json`
  after `zoom push 21.001,21.095`. Expected: every time in the record in the same base. Got: each
  marker's `start`/`end` is relative to the profile zero (21001.09 for a marker listed at t=21.001s),
  but `context.currentViewRange` is absolute (start 21008.73, i.e. 21.001s + the 7.73 ms
  `rootRange.start`). For a moment the zoom looked like it excluded markers the list showed inside it.
  Workaround: subtract `rootRange.start` from `currentViewRange`. Could use one base, or name the field
  `currentViewRangeAbsolute`.
- **Question: "when was this task dispatched?"** `thread markers --search <task id> --list` joins a
  `Runnable` marker's `task` field to the `TaskController::AddTask` whose `flow` has the same id. It
  worked, but only after I found that the `task` field and the `flow` field are the same id. Also,
  pointer reuse gives one id to unrelated tasks (the same id shows up on vsync and IPC runnables tens
  of ms apart). `marker info` on a `Runnable` could show the nearest earlier `AddTask` with the same
  flow on that thread, and say when none was recorded there (dispatched from an unprofiled thread,
  such as a timer).
