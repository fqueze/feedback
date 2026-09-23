## Why is a process missing from the profile?

- Question: the embedder process (the pid the parent sends `PWindowGlobal::Msg_MakeFrameRemote` to, 3763 / 5062 / 3159) is absent from `profile info --all`, and it is the one process whose main thread would explain the failure.
- Command: `profiler-cli profile info --all --session <s>` (no row for the pid), `profiler-cli profile meta --json` (nothing either).
- Expected: a line saying the process was left out, and why.
- Got: nothing. The answer is in the profile's `profileGatheringLog`: `"Discarded child profile that would make the full profile too big, pid and size:", 3763, 198465344`.
- Workaround: downloaded and decompressed the 1.1 GB raw profile and parsed `profileGatheringLog` in Python (the key sits at the very end of the file). About 10 minutes and a 1.1 GB scratch file.
- What would have shown it: `profile info` (or `profile meta`) listing the gathering log's discarded/timed-out pids with their sizes.

## Delay between an IPC message and the message that answers it, per occurrence

- Question: for each `PWindowGlobal::Msg_MakeFrameRemote` the parent sent, how long until the next `PBrowserBridge::Msg_Show` from the same pid; and which of those fell inside that pid's `PIdleScheduler::Msg_StartedGC` → `Msg_DoneGC` interval.
- Command: `profiler-cli thread markers --search Msg_MakeFrameRemote --list --limit 0 --json` and the same for `PBrowserBridge::Msg_Show` and `PIdleScheduler`, paired in a Python script.
- What its output could have shown: an "IPC round trips" view pairing an outgoing message with the reply (`Reply_MakeFrameRemote` exists here) or with a named follow-up message, giving count / median / p90 / max and the slow occurrences with handles; and per child pid, the GC intervals it reported to the parent.
