## Question: "which Android xpcshell slot did this test run in, and which test used that slot just before?"

- Command: `profiler-cli thread markers --session <id> --search "launch_application,Launched Test App,Application ran for" --list --limit 0 --json`, then a Python script pairing each `XpcshellTestRunnerService$iN` in `launch_application` messages with the `_TEST_NAME` in the same message and with the `test` interval markers.
- Expected: some way to group/pair markers by a regex capture from their message (here the slot number), e.g. `--group-by field:message~'Service\$i(\d+)'`.
- Got: the default output truncates the long `launch_application` message before the slot and test name, so the list alone could not answer it.
- Workaround: script over `--json`.

## (review) Question: "which app processes did the harness's `ps` list at time t?"

- Command: `profiler-cli thread markers --session <id> --search get_process_list --list --limit 0` inside a `zoom push 176,186`.
- Expected: each row showing the part of the message that matched, or its tail. The app processes come at the end of mozdevice's roughly 5 KB `get_process_list: [[1, 'init', 'root'], ...]` DEBUG message.
- Got: every row cut off after `init`/`kthreadd`, so the list could not show whether `:xpcshell5` was running.
- Workaround: `--json` and a script. Also, a zoom plus `--search "test_runner:xpcshell5'"` (0 matches) against `--search get_process_list` (11 matches) answers it without a script, but nothing in the output points to that approach.
