## Question: the millisecond order and spacing of a test's log lines

- Command: `profiler-cli thread markers --session <s> --search test_group_touchevents-5 --list --limit 0` on a resource-usage profile (22-minute job).
- Expected: timestamps precise enough to order log lines tens of ms apart, e.g. `t=894.491s`.
- Got: `t=14m54s` on every row from the FAIL to the late `Got a touchmove` lines, so the list could not show that the touchmoves came 65 ms after the check.
- Workaround: `--json` and a script printing `flatMarkers[].start` (ms). A `--list` time format that keeps sub-second precision on long profiles (or a `--precise-times` flag) would have answered it.

## Same question, hit again by the review

- `thread markers --list` on all three resource-usage profiles printed `t=14m54s`-style times, so the ms order of the FAIL against the late touchmoves again needed `--json` and a script over `flatMarkers[].start`.
