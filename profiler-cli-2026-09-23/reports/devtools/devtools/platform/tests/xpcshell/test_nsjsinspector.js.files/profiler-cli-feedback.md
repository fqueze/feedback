## `thread markers --list` times are rounded to the second

- Command: `profiler-cli thread markers --session test_nsjsinspector.js-1 --list --limit 0 --search "Cleaning up profile"` (inside `zoom push 495,503`)
- Expected: start times precise enough to order harness log lines 50-500 ms apart (e.g. `t=497.561s`).
- Got: `t=8m18s` for every row in the same second, so the order of setup, `pidof`/`ps` and failure lines could not be read.
- Workaround: `marker info m-a m-b ... --json` and read `start`.

## Question needing a script: "which xpcshell slot each test ran in, and what the Android process list said at each moment"

- Commands: `thread markers --search "launch_application,Launched Test App,Application ran for,..." --list --limit 0 --json` and `--search get_process_list --json` (16 and 49 MB), then Python to pull `XpcshellTestRunnerService$iN` and `_TEST_NAME` out of `launch_application` payloads, and to diff pid names across `get_process_list` payloads.
- What default output could have shown: nothing generic, probably; the answer lives in harness-specific payload text. A `--field message --grep <regex>` style extraction, or a payload-substring column, would have avoided dumping 65 MB of JSON.
