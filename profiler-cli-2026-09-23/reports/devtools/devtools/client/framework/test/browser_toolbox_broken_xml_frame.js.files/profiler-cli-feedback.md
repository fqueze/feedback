## Two `--search` flags on `thread markers`: the first is silently dropped
- Command: `profiler-cli thread markers --session S --search "still alive" --search "-name:IO,-name:CPU Use" --list`
- Expected: both applied (AND), or an error saying to use one comma-separated `--search`.
- Got: only the last `--search` applied; the listing was every non-IO marker, so it looked like everything matched "still alive".
- Workaround: one `--search "still alive,-name:IO,..."`.

## Question: "what did the parent exchange with child pid X, by message type, and when did it stop?"
- Command: `thread markers --search "otherPid:268" --list --limit 0 --json` + python to group by (direction, messageType) with first/last time.
- The default list was 6,309 rows, 6,138 of them `PVsync::Msg_Notify`; the answer (last message received from the child at t=212.618s, FlushFOGData never answered) needed a script.
- Could have shown: `--group-by field:messageType` combined with direction and first/last timestamps per group.

## Question: "which child processes did the parent know about but are absent from the profile?"
- A content process (pid 268) that hung was launched, talked to over IPC, and is absent from `profile info`/`thread list`. Found it only by reading `Subprocess Priority` and IPC `otherPid` values by hand.
- Could have shown: in `profile info`, a line listing pids seen in IPC markers / Subprocess Priority markers that have no process in the profile.

## `profile info` gives process start/end as timestamp handles only
- Command: `profiler-cli profile info --all`
- Got: `[ts-q → ts-qe]`; needed `--json` + python to get seconds to see which processes were alive at t=212s.
- Expected: seconds next to the handles.

## Resource-usage profile: machine CPU over time has no summary
- The profile has no counters; CPU is in `CPU Use` markers whose `cpuPercent` is a string ("47.9%"). Needed `--json` + python to bucket it.
- Question: "was the machine busy while the test waited?" Could have shown: `counter`-like over-time buckets for `CPU Use` markers.

## `--search "phase:transferStart"` matches nothing though IPC markers carry `phase`
- Command: `thread markers --search "phase:transferStart,phase:transferEnd" --group-by field:messageType` → 0 markers; `marker info --json` shows `phase` under `rawFields`.
- Expected: raw payload fields searchable, or an error saying `phase` is not a searchable key.
