# profiler-cli feedback (test_bug481335.xhtml)

## Default session dir not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=test_bug481335.xhtml profiler-cli load "<taskcluster url>" --session test_bug481335.xhtml-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (the error message suggested it, which helped). One wasted load.

## IPC markers in `thread markers --list` show no message type

- Question: which `IPCIn`/`IPCOut` rows are `PContent::Msg_NotifyVisited` / `Msg_StartVisitedQueries`, and which process was on the other end?
- Command: `profiler-cli thread markers --search "NotifyVisited,..." --list --limit 0 --session ...`
- Expected: the IPC message type and peer (e.g. `PContent::Msg_NotifyVisited → http://mochi.test`) in the description column, as `Runnable` rows show their task name.
- Got: `IPCIn`/`IPCOut` rows with an empty description; had to pair them with the runnables by duration and run `marker info` on each to read `Type` and `From`/`To`.
- Workaround: `marker info m-A m-B ...` on the handles.
