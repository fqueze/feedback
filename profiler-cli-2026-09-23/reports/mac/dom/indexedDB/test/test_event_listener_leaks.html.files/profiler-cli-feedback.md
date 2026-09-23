## Default session directory not writable in the sandbox
- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session test_event_listener_leaks.html-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a hint to set PROFILER_CLI_SESSION_DIR.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (profile-link.py too). The hint was good; one wasted load.

## Daemon died silently on samples-top-down --include-idle in a zoomed range
- Command (session test_event_listener_leaks.html-1, t-0 parent GeckoMain selected, zoom pushed onto a 21 ms Runnable marker m-11754 inside a zoom 8.993,9.016): `profiler-cli thread samples-top-down --include-idle --limit 40` then `profiler-cli thread info`
- Expected: the call tree of the idle-priority runnable (the one without --include-idle printed an empty "Top-Down Call Tree:" with no message saying there were zero samples).
- Got: the `--include-idle` call printed only the header, the next command hung for 30 s with "Timed out ... waiting for the daemon", and `status` then said the daemon exited without cleaning up. The daemon log ends with "Received message: command" and no error.
- Workaround: reload the profile. An empty tree should say "0 samples in range" rather than print a bare header.

## Question: "which IPC message is this IPCOut/IPCIn row?"
- Command: `profiler-cli thread markers --search "PWindowGlobal::Msg_Destroy,DiscardBrowsingContext" --list --limit 0` (content GeckoMain)
- Expected: IPCOut/IPCIn rows labelled with their message type, as Runnable rows are ("PWindowGlobal::Msg___delete__ - priority: ...").
- Got: the search matched the IPC markers (on the messageType field), but their rows have an empty description column, so the three IPCOut rows at t=8.993s are indistinguishable; `marker info` on each was needed to learn one was PWindowGlobal::Msg_Destroy.
- What the default output could show: the messageType (and ideally the other side's handling time, endTime vs recvEndTime) in the list row.

## Question: "what did the CC free, step by step?"
- Commands: `thread markers --search name:CC --list --json` gave no field values, so `marker info <handles...> --json` plus a script (cc_table.py in this directory) was needed to tabulate Refcounted/GC Objects Visited/Freed per CC across a range.
- What the default output could show: a `--fields` option on `--list` (e.g. `--fields mFreedRefCounted,mFreedGCed`) or the marker's own description text for CC markers.
- Also: in zsh, passing a handle list as one unquoted variable fails with "Invalid marker handle m-1 m-2 ..."; accepting a space-separated single argument would help scripts.

## (review) Question: "which marker does this link's marker=N select?"
- Commands: `profiler-cli load "<profiler.firefox.com link with marker=116531>" --session ...`, then `thread markers --search ... --list` and `marker info <m-…> --json` on each candidate until one reported `markerIndex` 116531. Repeated for 20 links over 3 profiles, reloading P1 and P2 because handles die with the daemon.
- Expected: `load` to print the marker the link selects, as it prints the thread and range, or `marker info` to accept a raw index (`marker info idx:116531`).
- Got: `load` reports the selected thread and view range only. `--list` rows and `--json` `flatMarkers` have no `markerIndex`, so every check cost one `marker info` call.
- What the default output could show: "Selected marker: m-42 (IPCOut PWindowGlobal::Msg_Destroy, 8.993s)" after loading a link.

## (review) IPC rows with negative durations
- Command: `thread markers --search "PWindowGlobal::Msg_Destroy" --list` in the Windows profile dm-h_-Y2ShmzXTDKHt0FXw
- Got: `IPCOut ... -3.706ms`. `marker info` then shows a `recvEndTime` earlier than `sendStartTime`, which looks like clock skew between processes. Nothing flags it, so a reader can take the other side's timestamps as exact.
- What the output could show: a note on an IPC marker whose cross-process timestamps run backwards.
