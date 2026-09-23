## Question: "how long between these two log lines in a 25-minute resource-usage profile?"

- Command: `profiler-cli thread markers --search test_close.js --list --limit 0 --session <s>` on a job's resource-usage profile (25m1s long)
- Expected: timestamps precise enough to measure 26 ms, 40.5 s, 3 ms gaps between the test's own markers.
- Got: `t=5m38s` / `t=6m19s`: rounded to the second, so "ready 26 ms after pending" and "warning 3 ms after exiting test" cannot be read.
- Workaround: `--json` and `start/1000` in a python one-liner. Showing ms (e.g. `t=338.001s`) in `--list`, at least with `--search`, would answer it.

## Network marker end past the profile end

- Command: `profiler-cli marker info m-1 --session <s>` on DLL3QSNZRnGHYSeDLZtMZg's per-test profile
- Got: `Time: 539.62ms - 30.818s` while `profile info` says `Full: 30.531s`, and the raw `startTime`/`connectStart` fields use another origin (about 28 ms off) than the `t=` column. I had to work out the offset to compare `connectStart` with other markers. Printing the timing fields converted to profile-relative time would avoid that.
