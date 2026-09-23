## Marker times past 60 s lose all sub-minute precision

- Command: `profiler-cli thread markers --category Test --search browser_resources_sources --list --limit 0 --session browser_resources_sources.js-1` (and `marker info m-11`)
- Expected: `t=60.027s` for the TEST-UNEXPECTED-FAIL marker.
- Got: `t=1m` for every marker between 60 s and 61 s, in both `--list` rows and `marker info` ("Time: 1m (instant)"), so ordering of ~40 markers in the last second was unreadable.
- Workaround: `marker info m-11 --json` and read `start`.

## Question: which IPC messages did the parent exchange with process N?

- Command: `profiler-cli thread markers --search "otherPid:1600" --list --limit 0 --session browser_resources_sources.js-1`
- The filter works, but each row reads only `IPCIn`/`IPCOut` and a duration: the `messageType` field (the only discriminator, e.g. `PRemoteWorkerDebuggerManager::Msg_Register`) is not printed. Same with `profile markers --search Msg_Register`: 1229 matches, mostly IPC rows with no visible text, so it was impossible to see which of them matched and why.
- Workaround: `--json` and a Python script over `flatMarkers[].fields` to print `messageType` per marker.
- What the output could have shown: the IPC marker's `messageType` (and direction/other pid) as the row description, as the profiler UI does.
