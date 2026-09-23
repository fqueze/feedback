## Question: how many ms after the content process launch did the crash happen?
- Command: `profiler-cli thread markers --session <s> --list --limit 0 --search "Process Immediate Launch,oop-frameloader-crashed"` on a 3m34s profile.
- Expected: start times precise enough to order events within a second (e.g. `t=114.307s`).
- Got: `t=1m54s` / `t=1m55s` for everything; the 230 ms launch-to-crash gap, and the order of events inside one second, are not visible.
- Workaround: `--json` and reading `start` from `flatMarkers`. Could have shown `t=1m54.307s` in the list.

## Question: which IPC messages did the parent exchange with pid 8232, and in which direction?
- Command: `profiler-cli thread markers --session <s> --list --limit 0 --search "otherPid:8232"`.
- Expected: one row per message with its type, direction and the other side, like the `marker info` view gives ("Type: PContent::Msg_LoadURI", "Other Pid: 8232").
- Got: rows reading only `IPCOut` / `IPCIn` with an empty description column, so the list cannot answer "did the child ever send anything, and what".
- Workaround: `--json` and a script over `fields[].messageType`. The description column could carry `messageType` and `otherPid` (and sync/direction).

## Question: what were the machine's CPU and memory at the moment of the crash (resource-usage profile)?
- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use,name:Memory" --list --limit 0` after `zoom push 148.5,154.5`.
- Expected: the CPU percent and memory used in each row.
- Got: 122 rows of `CPU Use` / `Memory` with durations and no values; `counter list` says "No counters in this profile".
- Workaround: `--json` and reading `data.cpuPercent` / `data.used`. The description column could show `cpuPercent` and `Memory Used`.

## Question: which IPC message did search "oop-frameloader-crashed" match?
- Command: `profiler-cli thread markers --list --search "PContent::Msg_InitCrashReporter,Process Immediate Launch,oop-frameloader-crashed"`.
- Got: `IPCIn` rows (which are the InitCrashReporter messages, matched through `messageType`) with no description, so in the list they looked like unexplained matches and I first mislabelled them as crash notifications.
- Same fix as above: show `messageType` for IPC markers in `--list`.
