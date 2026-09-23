## Question: "which child process did the parent talk to that has no profile of its own (crashed / never started)?"
- Command: `profiler-cli thread markers --category IPC --group-by field:otherPid --session <s>` (after zoom), then `thread markers --search otherPid:8064 --list`.
- Expected: `profile info` (or a dedicated line) to flag pids the parent exchanged IPC with that are absent from the process list, e.g. "pid 8064: 64 IPCOut, 0 IPCIn, no profile".
- Got: `profile info --all` lists only processes with a profile; the dead child showed only as an otherPid group in the IPC markers.
- Workaround: group IPC by otherPid and compare with the process list by hand.

## Question: "when did each child send its first message after launch?"
- Command: `profiler-cli thread markers --search otherPid:<pid> --list --limit 0 --json | python3 ...` (first IPCIn per pid).
- Expected: the default output could show first/last IPCIn/IPCOut times per otherPid in the `--group-by field:otherPid` view.
- Got: counts and durations only, so I had to script over `--json`.
