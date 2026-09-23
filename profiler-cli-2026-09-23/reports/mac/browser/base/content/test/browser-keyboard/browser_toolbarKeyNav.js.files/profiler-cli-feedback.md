## Question: which IPC messages did a content process send, in order, and when?

- Command: `profiler-cli thread markers --search IPC --list --limit 0 --session <s>` on a content GeckoMain thread (zoomed to 12 ms).
- Expected: each `IPCOut`/`IPCIn` row to name its message type (`PBrowser::Msg_OnStateChange`, `PWindowGlobal::Msg_RawMessage`, ...), as `Runnable` rows do with their task name.
- Got: `m-835  IPCOut  t=10.828s  2.971ms  ✗` with an empty description; the type is only in `marker info`.
- Workaround: `profiler-cli marker info m-810 m-816 ... --json` piped to a Python script to print `fields[].messageType` per handle.
- What the default output could show: the message type (and direction/peer) in the list's description column.

## `load` fails outside the default session dir with EPERM under a sandbox

- Command: `profiler-cli load <taskcluster url> --session browser_toolbarKeyNav.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command. Cost one round trip; the hint was good.

## Question: does this handle's marker match the `marker=N` of a link? (review-browser_toolbarKeyNav.js)

- Command: `profiler-cli marker info m-1296 m-1298 m-27 --session review-browser_toolbarKeyNav.js-1`
- Expected: the marker index next to the handle, since `review-brief.md` checks links by comparing it with `marker=N`.
- Got: text output without the index. Only `--json` has `markerIndex`.
- Workaround: `marker info … --json` piped to a Python script printing `markerHandle`, `markerIndex`, `name`, `start` for each record. Needed once per profile, for 4 to 6 handles each.
- What the default output could show: `Marker m-1296 (index 146025)` in the header line.

## Question: which of two events on different threads happened first, to the microsecond? (review-browser_toolbarKeyNav.js)

- Command: `profiler-cli thread markers --list` on the parent (`TaskController::AddTask` for `tryOnce`) and on the content thread (the `IPCOut` of `PBrowser::Msg_OnStateChange`).
- Expected: enough precision to order an `AddTask` against an IPC message's arrival 13 µs apart.
- Got: `t=10.828s` for both. The IPC phase times (`recvEndTime`, and so on) are only in `marker info`, as raw ms on a clock whose zero differs from the displayed `t` by 7.41 ms in this profile.
- Workaround: `marker info --json` with a script, and the offset worked out by hand by matching an IPC marker's `endTime` with the `start` of its receiving `Runnable`.
- What the output could show: IPC phase times converted to the profile's `t`, in `marker info`, and a `--precise` time column for `--list`.
