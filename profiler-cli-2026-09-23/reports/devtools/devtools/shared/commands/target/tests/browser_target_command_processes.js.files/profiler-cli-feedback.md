## Marker times of 59.5 s and later print as "1m"

- Command: `profiler-cli thread markers --category Test --search browser_target_command_processes --list --limit 0 --session <id>` (also `marker info m-16`)
- Expected: a time I can order against the others, such as `59.533s` or `1m0.105s`.
- Got: `t=1m` for every marker from 59.5 s up to the end of the profile (60.1 s). The
  TEST-UNEXPECTED-FAIL (59.533 s), both `ipc:content-shutdown` (59.476 s shows fine, 59.630 s shows
  as `1m`) and an AsyncShutdown blocker's end all read `1m`, so I could not tell whether the failure
  came before or after the second process shut down.
- Workaround: `marker info <handles> --json` and read `start`.

## Question: how busy was the machine over these 2 seconds?

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <resource-usage profile>` after `zoom push 1246.9,1249.0`
- Expected: each row showing its CPU percentage (the one field that matters for this marker), or a
  summary such as min/avg/max of `cpuPercent` over the zoom.
- Got: rows with only a handle, a time rounded to the second (`t=20m47s`) and a duration; the
  percentage is only in `marker info` or `--json`. `counter list` says the profile has no counters.
- Workaround: `--json` and a Python one-liner over `flatMarkers[].data.cpuPercent`.

## Review: hit both of the above again (review-browser_target_command_processes.js)

- Same two costs in the review. The first is wider than "1m": every time from 60 s on is rounded
  to the whole second. In J-zsS7DS (failure at 70.5 s), `releaseCachedProcesses` (69.485 s) and
  `Msg_Shutdown` (69.594 s) print as `1m9s` and `1m10s`. `ipc:content-shutdown` (70.461 s) and
  the TEST-UNEXPECTED-FAIL (70.540 s) both print as `1m10s`/`1m11s`, so they cannot be ordered
  without `--json`. The resource-usage `test` markers (`t=20m47s`) have the same problem.
- Workaround again: `marker info <handles> --json` and a Python one-liner reading `start`.

