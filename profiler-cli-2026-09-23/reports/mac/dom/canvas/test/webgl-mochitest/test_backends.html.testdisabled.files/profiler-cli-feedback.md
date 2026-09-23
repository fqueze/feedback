## Question: on which side of a cross-thread race did each marker fall, when they are microseconds apart?

- Command: `profiler-cli thread markers --list --limit 0` on three threads (parent main, GPU main, GPU CanvasRenderer) zoomed to 2.285,2.309, looking for whether `PWebGL::Msg_Initialize` on CanvasRenderer started before or after `PGPU::Msg_PreferenceUpdate` on the GPU main thread.
- Expected: start times precise enough to order markers on different threads (µs), and a way to merge several threads' markers into one chronological list.
- Got: every time printed as `t=2.297s`, so all the markers of the race (spanning 2296.868 to 2297.048 ms) looked simultaneous; and one list per thread.
- Workaround: a script over `thread markers --search … --list --json` for each thread, merging `start` (ms, full precision) into one sorted timeline (`timeline.py` in this directory). A merged multi-thread list (`--thread t-0,t-9,t-12`), and times printed with a precision that follows the zoom width, would have answered it directly.

## Question: which IPC message is this IPCIn/IPCOut row?

- Command: `profiler-cli thread markers --list --limit 0` on the GPU process threads.
- Expected: the message type (`PGPU::Msg_PreferenceUpdate`, `PWebGL::Msg_Initialize`) in the label column of IPC markers, as the profiler UI shows it.
- Got: label `IPCIn` / `IPCOut` / `SyncIPCIn` only, or empty; the type is only in `marker info`. Searching by the type (`--search PWebGL::Msg_Initialize`) did match them, which was the workaround.
- Also: the IPC payload's `sendStartTime` / `recvEndTime` are in a different time base from the displayed `t=` (offset 7.548 ms in this profile), so comparing them with other markers needs a manual offset.

## Question (review): are two processes' clocks aligned in this profile?

- Command: `profiler-cli marker info m-105 --json` (JKe_evEtRhKoGXwimWdfbg), after `thread markers --search IPC --list` on the content main thread showed IPC markers with negative durations (`-0.553ms`, `-0.631ms`) and no comment.
- Expected: a warning that the processes' time bases disagree, and by how much, since cross-process intervals ("sent at X, received at Y") are then wrong by that amount.
- Got: `PWebGL::Msg_GetString` "received" by the GPU process 0.60 ms before the content process sent it, `PContent::Msg_ScriptError` received by the parent 0.82 ms before it was sent; nothing flags it. In TCzj-Xg7S1uj7DQ6Y0Q7Ag the content process clock is off the other way.
- Workaround: bound the offset by hand from a request and its reply (`PWebGL::Msg_GetString` and `PWebGL::Reply_Initialize`): content runs 0.60–0.68 ms ahead of the GPU process. `profile info` could print a per-process offset estimated from paired IPC markers, and `marker info` could flag an IPC marker whose `recvEndTime` precedes `sendStartTime`.

## Session directory in the sandbox (review)

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session ...`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the error message named the fix.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every `profiler-cli` call. The skill's brief does not mention it.

## Linking from a session opened on a report's link (review)

- Command: `python3 profile-link.py --session review-test_backends.html.testdisabled-3 --marker m-105`, on a session loaded from the report's `profiler.firefox.com/from-url/...` link, as `review-brief.md` says to load it.
- Expected: a link for a new observation.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: stop the session and load the same profile again from its Taskcluster URL (one more 0.6 GB load). `profile-link.py` could take the artifact URL out of a `from-url` link, as the session already has it.
