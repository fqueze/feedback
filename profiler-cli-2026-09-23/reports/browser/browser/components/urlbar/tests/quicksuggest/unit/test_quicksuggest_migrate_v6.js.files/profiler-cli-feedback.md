## Machine CPU over a time range needs a script over `--json`

- Question: "was the machine saturated between t=150 s and t=240 s?" (the brief's "slow machine is shown only when the CPU tracks show it").
- Command: `profiler-cli zoom push 150,240 --session <s>; profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` on a resource-usage profile, then averaging `data.cpuPercent` weighted by duration in Python.
- What the default output could have shown: `thread markers --search "name:CPU Use"` (non-list) prints only duration stats of the markers; a min/avg/max of the numeric payload fields (cpuPercent) over the zoomed range would have answered it. `counter list` reports no counters in these profiles.

## Which test emitted each C++ warning needs `--json`

- Question: "which test's xpcshell printed each `Process N may be hanging at shutdown` / `hanging at shutdown; attempting crash report`?" (to see which neighbours' GPU children were stalled at the same moment).
- Command: `profiler-cli thread markers --search "hanging at shutdown,PGPU::Reply_Init,exited with status" --list --limit 0 --json` then printing `data.test` per marker.
- What the default output could have shown: for `cppDebug` markers the list row ends with `file:line`, while for `output`/`INFO` rows it ends with the test path; the `Test` field is only visible in `marker info`. Showing the test (or pid) on the `--list` row for C++ warnings too would have avoided the script.
- Minor: in `--list --json`, instant markers have no `duration` key at all (not `null`), which broke a first script with KeyError.
