## Marker times lose precision past one minute

- Command: `profiler-cli thread markers --session <s> --list --limit 0 --search "launch_application,Launched Test App,Application ran for,Cleaning up profile,Failed to start,name:test"` (zoomed to 195–216 s of a 28 min resource-usage profile).
- Expected: start times precise enough to order events that are 10–100 ms apart (`t=212.104s` or `t=3m32.104s`).
- Got: `t=3m32s` on every row in that second; `marker info` likewise prints `Time: 1m38s`. Ordering an adb check against the `ps` listings around it was impossible from the text output.
- Workaround: `--json` and a Python script over `flatMarkers[].start`.

## Question: "which harness slot / pid did each test use, over time" (Android xpcshell resource profile)

- Command: same as above, plus `--search get_process_list`.
- Needed a script because: the answer is a substring of long messages (`XpcshellTestRunnerService$iN`, `_TEST_NAME = "..."` inside a multi-KB `launch_application` INFO; the `org.mozilla.geckoview.test_runner:xpcshellN=<pid>` entries inside a multi-KB `get_process_list` DEBUG). `--list` truncates to the start of the message, so the matching part is never shown.
- What would have answered it: an option to print only the matched span (plus some context) of a long text field, e.g. `--show-match`, or a regex capture `--extract 'xpcshell\d+'`.
