# profiler-cli feedback — test_event_listener_leaks.html

## IPCIn / IPCOut list rows do not say which message they are

- Command: `profiler-cli thread markers --min-duration 5 --list --limit 60 --session test_event_listener_leaks.html-3` (parent main thread, zoomed on a 285 ms jank)
- Expected: each `IPCIn` row names its message type (e.g. `PWindowGlobal::Msg_Destroy`) and sender, the way `Runnable` rows name theirs.
- Got: about 30 rows of bare `IPCIn  t=10.131s  233.63ms ✗`, with no detail column. To find which one was the delayed `PWindowGlobal::Msg_Destroy`, I needed `thread markers --search "PWindowGlobal::Msg_Destroy" --list` (it matches the payload) and then `marker info m-110`.
- Question it could not answer by default: "which IPC messages were queued on this thread during this jank, and from whom?"

## A stopped session was listed again later

- Commands: `PROFILER_CLI_SESSION_OWNER=test_event_listener_leaks.html profiler-cli stop test_event_listener_leaks.html-1` printed `Session test_event_listener_leaks.html-1 stopped`. A few minutes later, `profiler-cli session list` still showed `test_event_listener_leaks.html-1`, "created at 17:52:58", owned by me, with a live daemon pid. The only other command naming that session was `profile-link.py --session test_event_listener_leaks.html-1`, run before the stop.
- Expected: a stopped session stays stopped.
- Workaround: ran `stop` again, and it stopped. With about 0.6 GB per daemon, a session silently coming back matters for the fleet's memory budget.
