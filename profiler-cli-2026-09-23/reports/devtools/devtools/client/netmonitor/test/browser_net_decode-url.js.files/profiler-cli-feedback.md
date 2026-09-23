## Which IPC messages went to one child pid, and did it ever answer?

- Question: a content process (pid 4584) never registered with the profiler; did it ever send the
  parent anything?
- Command: `profiler-cli thread markers --search 'otherPid:4584' --list --limit 0` gives 63 rows
  all reading `IPCOut ... 0s` with no message type or direction, so the answer needed
  `--json` and a script grouping by `data.direction`, `data.phase`, `data.messageType`.
- What the output could show: the message type and direction in the `--list` row for IPC
  markers, or a per-pid summary (sent / received counts, first received message such as
  `ACCEPT_INVITE_MESSAGE`).
