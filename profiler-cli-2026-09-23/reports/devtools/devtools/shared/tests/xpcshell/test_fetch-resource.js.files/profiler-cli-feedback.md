## `zoom push` silently misreads the times profiler-cli itself prints

- Command: `profiler-cli zoom push 11m38s,11m46s --session test_fetch-resource.js-1`
- Expected: a 8 s zoom, or an error: `11m38s` is the format `thread markers --list` prints for times past one minute.
- Got: `Pushed view range: ts-1 (11s) to ts-1 (11s) (duration: 0s)`, a zero-length range, no error.
- Workaround: convert to seconds (`zoom push 695,707`).

## Question needing `--json`: exact start and end of `test` markers after the first minute

- Question: which xpcshell slot did the test get? That needs which test started or ended first, to the millisecond, around t=700 s.
- Command: `profiler-cli thread markers --search "name:test" --list` prints `t=11m41s` with `2.028s` durations; the sub-second start is lost once t > 60 s, so the order of tests starting in the same second is not readable.
- Workaround: `--list --json` and a script over `flatMarkers[].start`/`duration`.
- Could show: millisecond start times in `--list` (e.g. `t=11m40.760s`), or a `--precise` flag.

## Question needing `--json`: full message of long INFO markers

- Question: which `XpcshellTestRunnerService$iN` and `_TEST_NAME` each `launch_application` marker had, and which pids each `get_process_list` DEBUG line listed under a given name.
- Command: `profiler-cli thread markers --search launch_application --list --limit 0` cuts each message at the terminal width, so `_TEST_NAME` (near the end of a 4 kB message) is never shown; `marker info` shows it but only for handles you already know.
- Workaround: `--list --json` and a regex over `fields[0].value`.
- Could show: a `--full-messages` flag on `thread markers --list`, like fx-tests has.
