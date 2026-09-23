## The daemon died silently between two commands

- Command: after about 10 successful queries, `profiler-cli thread select t-122 --session browser_target_command_tab_workers_bfcache_navigation.js-1`, then `thread info`.
- Expected: thread info.
- Got: `Session ... is not reachable. Nothing is accepting connections on ....sock. The daemon exited without cleaning up.` The session log (`~/.profiler-cli/<id>.log`) ends on `Received message: command`, with no error. The machine had 85 GB available.
- Workaround: reload the profile (about 1 min). The same commands then worked. Marker handles had changed, so every handle had to be looked up again.

## `marker info --json` returns `handle: null`

- Command: `profiler-cli marker info m-73 m-71 --json`.
- Expected: each record carries the handle it was asked for.
- Got: `handle` is None in every record. With several handles in one call, the records have to be matched back to their handles by position.
