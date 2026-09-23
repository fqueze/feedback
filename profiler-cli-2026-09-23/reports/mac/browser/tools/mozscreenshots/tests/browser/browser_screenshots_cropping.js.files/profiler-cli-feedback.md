## Question: which IPC messages did the main thread send during this range

- Command: `profiler-cli thread markers --category IPC --list --limit 0 --session crop-1` (zoomed on a 20 ms range).
- Expected: each IPCIn/IPCOut row naming its message type (`PCompositorManager::Msg_AddSharedSurface`) and peer, as the other marker rows show their text.
- Got: rows with a name, time and duration only; the message type was only in `marker info` fields, so I scripted `marker info m-277..m-300 --json` before finding that `--group-by field:messageType` answers it.
- Workaround: `thread markers --category IPC --group-by field:messageType`.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <taskcluster url> --session browser_screenshots_cropping.js-1`
- Expected: a load, or a failure before the daemon is spawned.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then a second error `Unknown session ... no metadata found` from the chained `profile info`. The message did say to set `PROFILER_CLI_SESSION_DIR`, which worked.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (profile-link.py honours it too).
