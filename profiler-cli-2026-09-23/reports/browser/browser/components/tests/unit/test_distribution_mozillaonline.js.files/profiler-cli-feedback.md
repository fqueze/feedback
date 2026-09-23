## Question: when exactly did each of these tests start and end, relative to each other?

- Command: `profiler-cli thread markers --session <s> --search "browser/components/tests/unit" --list --limit 0` on a 25-minute resource-usage profile.
- Expected: start times (and end times for interval markers) precise enough to order events that are milliseconds apart.
- Got: `t=6m7s` for every row (1 s resolution) and only a duration, so six overlapping tests and the failing tests' log lines (368.435 s, 368.482 s, 368.503 s, 368.536 s...) all read as the same instant.
- Workaround: `marker info m-26..m-31 --json` piped through a python script to print `start`/`end`.
- What would have answered it: millisecond precision in `--list` start times on long profiles (e.g. `6m7.013s`), and an end column for interval markers.

## Question (reviewer, same as above): at what millisecond did each test and log line happen in a 25-minute resource-usage profile?

- Command: `profiler-cli thread markers --session <s> --search test_distribution --list --limit 0` on the MNzW and QDGn resource-usage profiles.
- Expected: `--list` start/end precise to the millisecond.
- Got: `t=3m32s` / `t=6m7s` on every row, again; then `marker info m-1 m-4 m-15 --json` (several handles) did not return records with a top-level `start`, so a script written for the single-handle shape broke.
- Workaround: `thread markers ... --list --json` and printing `flatMarkers[].start` / `duration`, or one `marker info` call per handle.
- What would have answered it: millisecond start and an end column in `--list` on long profiles.
