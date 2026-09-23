# profiler-cli feedback

## Question: the exact start time of a marker past the first minute
- Command: `profiler-cli thread markers --session <s> --search test_dbgclient_debuggerstatement --list --limit 0`, and `profiler-cli marker info m-1 m-3 m-7 --session <s>`
- Expected: times precise enough to subtract two markers 1.5 s apart (e.g. `t=65.354s` or `1m5.354s`), as the list does below one minute (`t=46.463s`).
- Got: `t=1m5s` for the test marker, the INFO 29 ms later, and a C++ warning 1.46 s earlier — all rounded to the second, both in `--list` and in `marker info` (`Time: 1m5s - 1m50s (45.019s)`).
- Workaround: `marker info ... --json` and read `start` (65353.915 ms). The default output could keep millisecond precision past 60 s.
