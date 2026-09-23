## Times of a minute or more lose their milliseconds in `--list`

- Command: `profiler-cli thread markers --session hittest-general-1 --thread t-0 --category Test --list --limit 0`
- Expected: `t=83.638s` for markers past the first minute, as for earlier ones (`t=43.143s`).
- Got: `t=1m24s` (and `marker info` prints `Time: 1m24s (instant)`), so a marker at 83.48 s and one at 83.64 s read the same, and neither time can be given to `--range` or `profile-link.py --range`.
- Workaround: `marker info <m> --json` and read `start` (ms, on a base about 7.5 ms off the displayed times).

## Which of two markers on different threads came first, when they are less than 1 ms apart (review)

- Command: `profiler-cli thread markers --session review-browser_test_general.js-3 --list --search RefreshDriverTick` on t-0 and then on t-23, zoomed to 2.975,3.015
- Question: did the content's first `RefreshDriverTick` start before or after the parent's tick on the same vsync?
- Got: `t=2.994s` on both threads. Only `marker info <m> --json` showed `start` 2993.8735 (parent) and 2993.8798 (content), 7 µs apart. The same thing happened when I compared `document load complete` with `UpdateDimensions` at 84.2 s, where `--list` prints `t=1m24s` for both.
- Could have shown: sub-millisecond start times in `--list`, or a list that merges several threads (`--thread t-0,t-23`) sorted by start time.
