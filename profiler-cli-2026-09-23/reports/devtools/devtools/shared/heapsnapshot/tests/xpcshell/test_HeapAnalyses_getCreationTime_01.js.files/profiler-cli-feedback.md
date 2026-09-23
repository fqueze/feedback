## Question: "what was each process's name, per pid, over time" (Android xpcshell `get_process_list` DEBUG lines)

- Command: `profiler-cli thread markers --search get_process_list --list --limit 0 --session <s>`
- Expected: a way to see, at ms precision, which test-runner processes each ps listing contains, and when a pid changes name.
- Got: rows with `t=3m35s` (1 s resolution) and the 15 kB ps list cut at the terminal width, so neither the time order within a second nor the process list is readable. Two listings 47 ms apart (215.299 and 215.346 s), the one that decides this failure, look identical.
- Workaround: `--json` (52 MB for 1,021 markers), then a Python script to `ast.literal_eval` each list and track names per pid.
- What would have answered it: ms timestamps in `--list` (e.g. `t=215.299s`), and a `--full-messages`-style flag; a generic "field contains X" filter would still leave the per-pid tracking to a script.

## Question: "which test ran in which slot" (launch_application lines)

- Command: `profiler-cli thread markers --search launch_application --list --limit 0 --session <s>`
- Got: each message cut long before `XpcshellTestRunnerService$iN` is followed by the test's path (the path is in `_TEST_FILE`, ~3 kB into the message), and times at 1 s resolution.
- Workaround: `--json` and a regex over `data.message`.
