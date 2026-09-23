## Question: "in what order, to the millisecond, did these harness log lines happen?"

- Command: `profiler-cli thread markers --search takeCensus_05 --list --limit 0 --session <id>`
- Expected: start times precise enough to order markers within the same second (e.g. `t=591.993s`), since the question was whether one thread's `Timing out` came before or after another's `Launched Test App` and a device-side kill.
- Got: past one minute, the list rounds times to whole seconds (`t=9m51s`), so `Launched Test App` (591.239 s) and `Timing out` (591.993 s) both read `t=9m51s`/`t=9m52s`, and the order inside a second is lost.
- Workaround: `--json` piped to a python one-liner printing `flatMarkers[].start`. A `--precise-times` flag, or millisecond precision for instant markers in `--list`, would have answered it directly.

## Question: "which harness log lines mention pid 19466 or slot xpcshell8?" (review)

- Command: `profiler-cli thread markers --session <id> --search 'xpcshell8,19466,21686' --list --limit 0`
- Expected: the few dozen INFO lines naming those pids or that slot.
- Got: 1 MB of output. Every mozdevice `DEBUG get_process_list: [[1, 'init', 'root'], …]` marker contains every pid and process name on the device, and `--list` prints each one in full on a single line.
- Workaround: redirect to a file, then `grep -v get_process_list | cut -c1-400`. What would have helped: a per-row width cap in `--list`, or a hint that `-name:DEBUG` excludes those markers.
