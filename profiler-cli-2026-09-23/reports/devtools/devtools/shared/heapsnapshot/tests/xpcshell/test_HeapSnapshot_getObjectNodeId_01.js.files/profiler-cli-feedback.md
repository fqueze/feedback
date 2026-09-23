## Question: which Android service slot / pid did each xpcshell test launch on, and which processes did `ps` list at time t

- Command: `profiler-cli thread markers --session <s> --search "launch_application,get_process_list" --list --limit 0` on an Android xpcshell resource-usage profile.
- Expected: to read, per marker, the `XpcshellTestRunnerService$iN` slot and `_TEST_NAME` of each `launch_application` INFO marker, and the `org.mozilla.geckoview.test_runner:*` entries of each `get_process_list` DEBUG marker.
- Got: labels are cut to the terminal width even with a large COLUMNS (the launch_application label is ~3 KB, get_process_list ~15 KB), so the slot, test name and app processes (at the end of the label) are never visible.
- Workaround: `--json` and a Python regex over `flatMarkers[].label`. A `--full-labels` option, or a `--grep <regex>` that prints only the matching substrings of each label, would have answered it.

## (review) `--session` rejected before the subcommand

- Command: `profiler-cli --session review-getObjectNodeId_01-1 zoom push 188.4,190.9`
- Expected: the global option accepted in any position, as the brief's "pass `--session` on every call" suggests.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand's arguments.

## (review) Question: which names did one pid carry across a job's `ps` listings

- Command: `profiler-cli thread markers --search "get_process_list" --list --limit 0 --json` on an Android xpcshell resource-usage profile (1464 markers, 85 MB of JSON).
- Expected: to find the processes whose name changed from one `:xpcshellN` to another, which is the Android false-left-over signature.
- Got: only the full JSON answers it, and a script over it.
- Workaround: a Python regex over `flatMarkers[].data`. What would have answered it: a way to track one value over time, such as `--grep '\[26182, [^]]*\]'`, printing only the matching part of each marker with its time.
