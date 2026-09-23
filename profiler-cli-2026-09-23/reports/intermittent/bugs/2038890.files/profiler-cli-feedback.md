## 2038890: profile duration hides that markers/samples only cover the last minutes

- Command: `profiler-cli profile info --session 2038890-1` and `profiler-cli profile meta` on a CI per-test profile (profile_browser_smartwindow_manage_tabs_tool.js.json, task DE4kDmklSbqD6bksBdvBDw).
- Expected: to learn which time span actually has data on each thread.
- Got: "Full: 40m21s" / "Duration: 40m21s", but GeckoMain markers and samples only exist from t=34m8s (buffer wrapped); a `zoom push 0,1800` then `thread samples` returned "No samples in the current view". I first concluded that an event was absent from the session when it was only outside the retained window.
- Workaround: `thread markers --category Test --list --limit 5` to see the first retained marker.
- Question it could answer by default: "what time range does this thread have markers/samples for" — per-thread first/last marker and sample time in `profile info` / `thread info`.

## 2038890: IPC request/reply latency needed a script

- Question: "how long did each `PBackground::Msg_CreateFileSystemManagerParent` wait for its reply".
- Command: `profiler-cli thread markers --session 2038890-3 --search CreateFileSystemManagerParent --list --limit 0 --json` + python pairing IPCOut send markers with the `Reply_…` Runnable markers.
- The IPC markers carry `messageSeqno`, so profiler-cli could pair a message with its reply and print the round trip (e.g. `thread ipc --search X` with send→reply durations).
