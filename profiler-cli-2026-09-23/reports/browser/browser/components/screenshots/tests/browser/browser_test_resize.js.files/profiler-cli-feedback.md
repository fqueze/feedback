## Question: "in which order did these test-log lines happen within the same second?"

- Command: `profiler-cli thread markers --session browser-browser_test_resize.js-1 --category Test --search browser_test_resize --list --limit 0`
- Expected: timestamps precise enough to order markers (ms), as the profile is 1m46s long and the race is 24 ms wide.
- Got: `t=1m39s` for every marker in the same second; `marker info` also prints `Time: 1m39s`.
- Workaround: `--json` and reading `flatMarkers[].start`. Millisecond precision (e.g. `t=98.968s`) in the list would have answered it.
