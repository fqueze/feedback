## `thread markers --list` timestamps are rounded to the whole second

- Command: `profiler-cli thread markers --category Test --search browser_browser_toolbox_take_screenshot --list --limit 0 --session <s>`
- Question: in what order, and how far apart, did the test's log lines happen (e.g. "take screenshot succeeded" at 84.307 s vs "process closed" at 85.109 s)?
- Expected: millisecond timestamps in the `t=` column, like the profile's own precision.
- Got: `t=1m24s`, `t=1m25s` for everything — six events inside one second are indistinguishable, and their row order is not even chronological (m-16 listed before m-20 but its handle order differs).
- Workaround: `--json | jq '.flatMarkers[] | "\(.handle) \(.start) ..."'`.

## Question: how long did each Browser Toolbox process live, test by test?

- Command: `profiler-cli thread markers --category Test --search "Browser toolbox started,Browser toolbox process closed" --list --limit 0 --session <s>`
- Needed: the gap between consecutive matching markers. With `t=` rounded to the second (above), the list cannot give it.
- Workaround: `--json | jq` pairing `.flatMarkers[].start`. A `--delta`/"since previous" column on `--list` would have answered it directly.
