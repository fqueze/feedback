## Question: "in which order, to the millisecond, did these markers happen?"

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_glean_pwmgr_form_autofill_result --list --limit 0`
- Expected: timestamps precise enough to order events a few ms apart (the whole race here is ~2 ms wide).
- Got: every row printed as `t=2m19s`; 60+ markers spanning 1.3 s all share two or three distinct values, so the list cannot order a FormProcessed against a flush or an IPC message.
- Workaround: `--json` and a script printing `start` with two decimals. The list could print ms (e.g. `t=139291.68ms` or `2m19.292s`) once a zoom or the profile length makes second-level rounding lossy.

## Question: "which IPC messages did this thread send and receive between t1 and t2?"

- Command: `profiler-cli thread markers --session <s> --search "IPCIn,IPCOut" --list --limit 0` (zoomed to 80 ms)
- Expected: each IPC row naming its message type (`PContent::Msg_FlushFOGData`, `PWindowGlobal::Msg_RawMessage`) and the other side, as the profiler UI does.
- Got: bare `IPCIn` / `IPCOut` rows with no description; the type is only in `marker info` or `--json` (`data.messageType`).
- Workaround: `--json` piped through a script printing `data.messageType`, `direction` and `otherPid`.

## Question: "which threads carry this marker, and when?" (review-browser_glean_pwmgr_form_autofill_result.js)

- Command: `profiler-cli profile markers --search 'BaseMetricId(5646)' --session <s>` (profile with 302 threads, 2.5M markers on the parent main thread)
- Expected: the matches across threads.
- Got: the first call failed with `Timed out after 30000ms waiting for the daemon ... to answer`, and so did a `status` sent in parallel. The same command run again answered in seconds.
- Workaround: re-run it. A cross-thread search that takes longer than the client timeout could report that it is still working, or the client could wait longer for it.

## Daemon exited without a trace (review-browser_glean_pwmgr_form_autofill_result.js)

- Command: `profiler-cli zoom push 155.4002,155.4060 --session <s>` then `profiler-cli thread info --session <s>`, on the same 2.5M-marker parent thread, after about 15 earlier successful queries.
- Expected: the thread info for the zoomed range.
- Got: `Session ... is not reachable ... The daemon exited without cleaning up`. The daemon log ends with `Received message: command` and records no error, so it gives no cause (possibly memory).
- Workaround: reload the profile and redo the zoom. Logging the exit reason (signal, heap size at exit) would show whether it ran out of memory.
