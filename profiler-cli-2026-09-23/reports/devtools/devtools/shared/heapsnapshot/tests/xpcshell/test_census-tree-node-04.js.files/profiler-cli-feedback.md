## Question: "which Android xpcshell slot ($iN) did this test run in, and which process names did each pid carry over time?"

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0 --json` (70 MB for one job's resource-usage profile), plus `--search launch_application --json` (20 MB), then Python to parse the ps lists and the `XpcshellTestRunnerService$iN` / `_TEST_NAME` out of the labels.
- Expected: some way to ask the default output for a field inside a long Text marker, or a per-marker `--max-label` / grep-within-label, so that "ps rows matching `test_runner:xpcshell`" can be printed without dumping every 320-process listing.
- Got: the list view truncates each label, so the only way to see the ps rows was the full JSON.
- Workaround: `--json` and a script (`ps_analyze.py`, `slots.py` in this directory).
