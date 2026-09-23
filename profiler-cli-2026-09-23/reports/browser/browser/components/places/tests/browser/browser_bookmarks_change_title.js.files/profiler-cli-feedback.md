## `thread markers --list` rounds times to the second past one minute

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_bookmarks_change_title --list --limit 0`
- Expected: timestamps precise enough to order and zoom on (ms), e.g. `t=79.017s`.
- Got: `t=1m19s` for five markers spread over 600 ms, including the last step before a timeout; `marker info` also prints `Time: 1m19s (instant)`.
- Workaround: rerun with `--json` and read `flatMarkers[].start`.
- Question the default output could have answered: "at what ms did the test log its last step, so I can `zoom push` from there".

