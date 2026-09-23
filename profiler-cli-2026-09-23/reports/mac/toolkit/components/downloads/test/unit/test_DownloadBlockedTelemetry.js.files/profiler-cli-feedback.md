## Daemon dies silently on a large `--list --limit 0`

- Command: `profiler-cli thread markers --search "name:INFO,name:Runnable" --list --limit 0 --session test_DownloadBlockedTelemetry.js-4` on a 30 s xpcshell profile with 5.4 million markers (2.7 million `DummyEvent` runnables), piped into `rg`.
- Expected: either the list, or an error saying the result is too large.
- Got: no output at all and exit status 0 through the pipe; the daemon was gone, and the next commands said `Unknown session test_DownloadBlockedTelemetry.js-4: no metadata found`. The session log ends at `Received message: command` with no error.
- Workaround: reloaded the profile, and used `--search DownloadDoneResolve` and `--category Test` separately. My filter was too broad (the brief warns about it), but the silent death cost a reload and some confusion: an error like "N markers match; refusing --limit 0 above M" would have said what happened.
