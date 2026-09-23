## Question: which windows created during this test were still alive at shutdown?

- Command: `profiler-cli thread markers --session <s> --search "name:DOMWindow,name:DocShell" --list --limit 0` on a `profile_resource-usage.json`
- Expected: each interval marker's end time, or a filter for markers alive at a given time, so they can be compared with the `Completed ShutdownLeaks collections in process <pid>` marker.
- Got: the start time, rounded to the second once past one minute, and the duration. Past one minute, neither gives an end time precise enough to compare with the shutdown marker, which leaks.py's verdict depends on.
- Workaround: `--list --json` and a script computing `start + duration` for the parent-process markers that start inside the test. It showed that all 14 windows and 7 docShells ended 1.6-49.5 s after the ShutdownLeaks line.
