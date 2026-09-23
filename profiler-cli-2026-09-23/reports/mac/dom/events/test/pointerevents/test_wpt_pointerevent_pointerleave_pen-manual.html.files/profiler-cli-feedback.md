## Default session directory not writable in the sandbox
- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session test_wpt_pointerevent_pointerleave_pen-manual.html-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The hint was good.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. One wasted load.

## Question: "which IPC message is this IPCIn/IPCOut row?"
- Command: `profiler-cli thread markers --search "Synth,mouse,..." --list --limit 0` on the parent main thread.
- Expected: IPC rows to show their message type, as `Runnable` rows show `PBrowser::Msg_SynthMouseMoveEvent`.
- Got: bare `IPCOut  t=6.662s  156.44μs` rows, with nothing to tell a `PBrowser::Msg_SynthMouseMoveEvent` from anything else.
- Workaround: `marker info` on each handle, or `--search messageType:PBrowser::Msg_DispatchMouseEvent` once the type is known (from `--group-by field:messageType`). Several extra calls.
- What would have answered it: the message type (and direction/peer) in the `--list` description column for IPC markers.

## Question: "what event is a DOMEvent with an empty event type?"
- Command: `profiler-cli marker info m-2965` (DOMEvent, `Event Type: (empty)`, target `browser@...`, latency 14.3 ms).
- Expected: some hint of the internal event message (here most likely `eMouseEnterIntoWidget`).
- Got: an empty type. The list shows ` - browser@15686d600` with nothing before the dash.
- Workaround: none; inferred from the IPC that followed. Probably a Gecko marker limitation (no DOM name for internal messages) rather than profiler-cli's, but the list could at least say `(no DOM type)`.
