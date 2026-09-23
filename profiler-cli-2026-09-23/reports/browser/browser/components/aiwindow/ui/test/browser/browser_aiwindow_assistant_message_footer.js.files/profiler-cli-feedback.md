## Marker list times are rounded to the second, so same-second ordering needs --json

- Question: "in what order did these IPC messages happen, in the parent and in the Inference process?" (an ML engine race lasting 300 ms).
- Command: `profiler-cli thread markers --session S --search 'MLEngine,...' --list --limit 0`
- Expected: timestamps with millisecond precision (e.g. `t=225.816s`), since `--list` is the chronological view.
- Got: every marker from 225.5 s to 226.5 s printed as `t=3m46s`. The order *within* one thread is still the list order, but lining up two threads is impossible.
- Workaround: `--json` on each thread, a python script printing `start/1000` with 3 decimals, and a sort of both threads' output together. A `--list` that shows ms once times exceed a minute (or a `--precision ms`), plus a way to list markers from several threads merged in time order (`--thread t-0,t-25`), would have answered it directly.

## `profile markers --search localhost` floods the output with one huge pref value

- Command: `profiler-cli profile markers --search localhost --session S`
- Expected: the matching markers, each on one line of reasonable length.
- Got: `Preference Read network.proxy.autoconfig_url` printed in full, a PAC script of about 15 KB on a single line, among other `Preference Read` noise.
- Workaround: `--search 'localhost:,-name:Preference Read,-name:Preference Write'`. Truncating marker descriptions in list output (as `thread markers` does with COLUMNS) would avoid it.

## `profile logs` seems to ignore the zoom

- Command: `profiler-cli profile logs --session S --limit 50` with `zoom push 225.5,226.35` active.
- Expected: log entries in the zoomed range, or a header saying the zoom does not apply.
- Got: the header shows the zoomed view (`View: ts-r1→ts-r9 (850ms)`), but the entries are timestamped minutes earlier (from the start of the profile).
- Workaround: none needed here; those logs were not useful.

## (review) IPC markers in `thread markers --list` carry no message type or direction

- Question: "which IPC message did the parent main thread send right after this `ReceiveQuery`, and to which process?" (to tell which of three `MLEngine:Removed` queries posted on the new engine's MessagePort).
- Command: `profiler-cli thread markers --session S --search 'MLEngine:Removed,messageType:PMessagePort' --list --limit 0` inside a 5 ms zoom.
- Expected: each `IPCIn`/`IPCOut` row with its `messageType` (`PMessagePort::Msg_PostMessages`), sender and receiver thread, and phase, as the profiler UI's marker table shows.
- Got: rows reading only `IPCOut` / `IPCIn` with an empty description (and times rounded to the second, already logged above), so ReceiveData to the Inference process, a same-process relay, and a `Msg_Close` all look the same.
- Workaround: `--json`, and a script printing `data.messageType`, `sendThreadName`, `recvThreadName`, and `start` with 3 decimals. A default description such as `PMessagePort::Msg_PostMessages Parent → Inference` would have answered it directly. The same data also showed a ~1 to 1.5 ms offset between the Inference and parent clocks (from `sendStartTime`/`recvEndTime`), which a merged multi-thread list would need to point out.
