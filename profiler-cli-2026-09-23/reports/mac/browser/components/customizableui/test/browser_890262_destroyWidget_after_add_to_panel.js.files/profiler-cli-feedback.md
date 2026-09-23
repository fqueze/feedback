## `load` selects a content-process thread by default

- Command: `profiler-cli load <taskcluster URL of profile_browser_890262_destroyWidget_after_add_to_panel.js.json for task aA8EsY8uRCC7LH3g6ljv0A> --session ...-3`, then `thread markers --search "mousedown,mouseup" --category DOM --list --limit 0`
- Expected: the parent process GeckoMain selected (t-0), as happened for another profile of the same test from another task.
- Got: `t-12 (GeckoMain, Privileged Content)` was selected, and the query answered "No markers match the specified filters." It looked like the events were missing.
- Workaround: `profile info --search GeckoMain`, then `thread select t-0`.

## Default session directory not writable in a sandbox, and no hint in `guide`

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session ...`
- Expected: the session loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message suggests `PROFILER_CLI_SESSION_DIR`, which worked, but it cost a round trip.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`.

## (review) `thread markers --list` prints times past 60 s as `t=1m14s`

- Command: `profiler-cli thread markers --search "name:RefreshDriverTick" --list --limit 0 --session review-890262-3` on the 901207 per-test profile (95 s long), zoomed to 74.25–74.45 s
- Expected: millisecond times, as below 60 s (`t=16.934s`), to find which tick was the last to name an observer.
- Got: every row read `t=1m14s`, so the order of events within that second could not be read. A text filter on `t=` also broke silently, which hid a keydown at 74.09 s.
- Workaround: `--json` and a script over `flatMarkers[].start`.
