# profiler-cli feedback (test_menubar.xhtml)

## Question: the precise time of each line of a test's log
- Command: `profiler-cli thread markers --session <s> --category Test --search test_menubar --list --limit 0`
- Expected: times precise enough to order the test's steps (ms), as the test log spans 5 minutes and steps are ms apart.
- Got: `t=5m8s` for every step of the first second, `t=10m10s` for all the post-timeout lines; no way to tell 307.567 from 307.620.
- Workaround: `--json` and read `flatMarkers[].start` (ms). Could have shown seconds with ms (e.g. `t=307.620s`) once the profile is longer than a minute, or at least when several listed markers share one printed value.
