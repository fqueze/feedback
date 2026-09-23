## Question: which IPC messages did the parent send to one child process after a given event

- Command: `profiler-cli thread markers --search 'otherPid:1476' --list --limit 0` (parent main thread, zoomed on 2.0515–2.0575s), then the same with `--json` and a Python script.
- Expected: each `IPCIn`/`IPCOut` row in the list to show its message type and direction/pid, as `Runnable` rows show `PContent::Msg_ScriptError`.
- Got: rows like `m-250  IPCOut  t=2.052s  0s  ✗` with an empty description, so the list could not say that one of them was `PProcessHangMonitor::Msg_RequestContentJSInterrupt` to pid 1476, the evidence that the parent was shutting the process down. Needed `--json` and a script over `flatMarkers[].data.messageType`, or one `marker info` per row.
- Workaround: `--json` plus a script; `marker info m-N` on the candidate.

## Default session directory not writable in the agent sandbox

- Command: `profiler-cli load <url> --session …`
- Got: `Cannot create the profiler-cli session directory /Users/florian/.profiler-cli … EPERM`. The message suggested `PROFILER_CLI_SESSION_DIR`, which worked; one wasted call.
