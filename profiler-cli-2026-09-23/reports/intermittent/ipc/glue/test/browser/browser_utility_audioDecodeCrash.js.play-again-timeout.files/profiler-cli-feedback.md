## Question: "which IPC messages did this thread send and receive in this window, and which request never got its reply?" (browser_utility_audioDecodeCrash.js, play-again timeout)

- Command: `profiler-cli thread markers --search "PRemoteDecoder,EnsureUtility" --list --limit 0 --session S`
- Expected: each IPCIn/IPCOut row to show its message type (`PBackground::Msg_EnsureUtilityProcessAndCreateBridge`, `PRemoteDecoder::Reply_Decode`) and the other pid, like the marker table's description column.
- Got: rows read only `IPCOut  t=4.878s  -367,262ns` with an empty description; `--search` does match on the message type, but the list never prints it. Workaround: collect the handles from `--list --json`, then `marker info <handles> --json` and read `fields[].messageType` / `otherPid` (script `ipc-types.sh` in this directory).
- Also: IPC markers recorded for other threads of the process (threadId in the payload) are listed under the main thread with no hint of that; a `sendThreadName`/`recvThreadName` column would say it.
