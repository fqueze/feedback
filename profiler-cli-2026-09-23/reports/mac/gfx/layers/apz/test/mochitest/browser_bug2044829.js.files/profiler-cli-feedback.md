# profiler-cli feedback (browser_bug2044829.js)

## Question: which IPC message is each IPCIn/IPCOut row, and from which process?

- Command: `profiler-cli thread markers --search "name:IPCIn,name:IPCOut,-name:SyncIPCIn,-name:SyncIPCOut" --list --limit 0 --session browser_bug2044829.js-1` on the GPU process main thread (t-17), zoomed to 3.64-6.66 s.
- Expected: each row naming the message type (`PWebRenderBridge::Msg_SetDisplayList`) and the peer process (`from pid 4271`), as the `Runnable` rows name their message.
- Got: 135 rows reading only `IPCIn` / `IPCOut` with a time and duration; the type and peer are only in `marker info`, one handle at a time.
- Workaround: `marker info <h1> <h2> ...` on a hand-picked list of handles. A script listing all `IPCIn` handles with `--json` and piping them to `marker info --json` did not finish in 120 s on the compositor thread (105,000 `SyncIPCIn` markers match `name:IPCIn` as a substring), and I killed it.
- What would have answered it: the IPC `messageType` and `otherPid` (or the process name) in the `--list` description column, like `Runnable` rows already carry their message name.

## `--search` with only exclusions matches nothing

- Command: `profiler-cli thread markers --search "-name:Awake,-name:Preference Read,-name:SyncIPCIn" --list --limit 0 --session browser_bug2044829.js-1` on t-7, zoomed to 60 ms.
- Expected: every marker except those names.
- Got: `0 markers (filtered from 2676)`, no hint why.
- Workaround: add a positive term list. The help says positives are OR'd then exclusions applied, so an exclusion-only search reads as "match nothing"; a note in the output ("no positive terms: nothing to exclude from") would have saved a retry.

## Question (review): what is marker index N on thread T?

- Context: the review brief checks a link's `marker=N` against `marker info --json`'s `markerIndex`. Three links (6317, 534500, 5767) did not match the `IPCIn` I had found. They turned out to be the `Runnable` marker that sits next to each IPC message.
- Command: no command takes an index. Workaround: `zoom push` to a window about 0.1 ms wide around the marker, list it with `--search "IPC,Runnable" --json`, then `marker info <every handle> --json` to read each `markerIndex`.
- What would have answered it: `marker info --index N` (or `thread markers --index N`) on the selected thread.

## Question (review): which process did each APZ IPC go to? (same as above, second occurrence)

- Command: `thread markers --search "NotifyAPZStateChange,messageType:NotifyAPZStateChange" --list` on the GPU Compositor thread.
- Got: `IPCOut` rows with no message type or peer. Only `--json` with `data.otherPid` showed that the failing touch's `PAPZ::Msg_NotifyAPZStateChange` went to the parent process and the passing touch's went to the content process. That turned out to be the key observation.
