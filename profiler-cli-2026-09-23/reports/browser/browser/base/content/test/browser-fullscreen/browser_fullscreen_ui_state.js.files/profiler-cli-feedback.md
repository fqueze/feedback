## IPC markers in `thread markers --list` show no message type

- Command: `profiler-cli thread markers --search IPCOut --list --session <s>` (zoomed on 12.2–12.23s)
- Expected: each row labelled with its IPC message type (e.g. `PContent::Msg_ConstructBrowser`) and the other process, as the profiler UI does.
- Got: rows showing only `IPCOut  t=...  duration`, with an empty description. To find which message a row was, I had to run `marker info` one row at a time, or guess the type name and pass it to `--search`.
- Workaround: `--search "PContent::Msg_ConstructBrowser,PBrowser::Msg_RemoteIsReadyToHandleInputEvents"`. It matches the Type field, but only if you already know the name.

## Was this thread on CPU during this range? (review)

- Command: `profiler-cli thread info --session <s>` on t-11 (Web Content, pid 5972) zoomed to 12.106,15.2 in the NVSN profile, then `profiler-cli thread list --session <s>`.
- Expected: one CPU figure for the thread, or a note that the CPU is unknown where samples carry no CPU delta.
- Got: `thread info` says "100% for 3077.4ms (12.106s - 15.185s)", while `thread list` gives the same thread 51.219ms of CPU for the whole profile. The raw profile shows why: the 194 samples in that range (taken before the Gecko profiler starts in that process, during its xul.dll load) all have `threadCPUDelta: null`, and `thread info` counts them as 100%. The original report took the 100% as measured.
- Workaround: downloaded the profile and summed `threads[11].samples.threadCPUDelta` in Python. `thread info` could say "CPU unknown (no CPU delta)" for such samples, and `counter info` for the process CPU counter already shows it starts only at 15.153s.
