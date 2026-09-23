## Question: which process existed on the device, and which harness slot each test used, around one instant

- Command: `profiler-cli thread markers --session <s> --search "name:DEBUG,name:INFO" --list --limit 0` on an Android xpcshell resource-usage profile, zoomed to a few seconds.
- Expected: to read which `org.mozilla.geckoview.test_runner:xpcshellN` processes the `get_process_list: [...]` DEBUG markers list, and which `XpcshellTestRunnerService$iN` each `launch_application:` INFO marker used, next to the time.
- Got: each message cut to the terminal width, so the process list (300+ entries) and the `$iN`/`_TEST_NAME` parts of the 3 KB launch command are cut away; `--json` has them in `data.message`, but not the start time in seconds in the same shape as the text output (ms floats).
- Workaround: a Python script over `--json` extracting the regexes. A `--full-messages` flag on `thread markers --list`, or a `--grep <regex>` that prints only the matching part of each message, would have answered it.
