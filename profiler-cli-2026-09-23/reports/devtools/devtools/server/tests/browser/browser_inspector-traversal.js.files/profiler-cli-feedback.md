## Question: which child processes are missing from this profile, and why?

- Command: `profiler-cli profile info --all --session browser_inspector-traversal.js-1` (per-test profile of task ARUp7AUORdi6qzwpfDkKDA).
- Expected: some sign that a live child process (pid 6664, ContentParent id=138) is absent because it never answered the gather request.
- Got: nothing. The only way to learn it was to download the raw profile and grep `meta.profileGatheringLog`, which says `Waiting for pending profile, pid: 6664` ... `No progress made recently, giving up; pending pids: 6664`.
- Could have shown: `profile info` / `profile meta` printing the gathering log's pending/timed-out pids ("1 child did not answer: pid 6664"). For a hang, that line is the main evidence.

## Question: are these two processes the same one? (pid reuse on Windows)

- Command: `profiler-cli profile info --all --json` then `thread list`.
- Got: two distinct http://example.org content processes that both had pid 3080 are shown as one process p-105 with threads t-116 and t-131; the process start/end times are those of the first one only. Also, the parent-side startup IPC markers (ACCEPT_INVITE_MESSAGE, PContent::Msg_InitCrashReporter, PProfiler EnsureStarted) for a second process with a reused pid (6664) only appear once, at the first process's time; possibly an IPC pairing artifact keyed on pid+seqno.
- Workaround: compared thread tids and marker times by hand.
- Could have shown: separate process entries per (pid, start time), or a warning that a pid was reused.

## Question: was the machine busy during this time range? (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` in a zoom, then a python script averaging `cpuPercent`.
- Got: one row per 100 ms marker; no aggregate.
- Could have shown: mean/median/max CPU% for the zoomed range, e.g. a `counter info`-style summary for the CPU Use markers.
