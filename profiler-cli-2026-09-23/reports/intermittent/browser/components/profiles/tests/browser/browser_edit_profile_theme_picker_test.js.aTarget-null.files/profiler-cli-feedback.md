## Marker times past 60 s lose their precision

- Command: `profiler-cli thread markers --session aTarget-null-3 --category Test --search browser_edit_profile_theme_picker_test --list --limit 0`, then `profiler-cli marker info m-35 m-36 m-37 m-38 m-39 m-16`, on a profile 69 s long (dlo0CgOeSYevvaM5PCTYhw, Windows asan).
- Expected: times such as `t=64.623s`, as for markers before 60 s.
- Got: `t=1m4s` and `t=1m5s` for every marker after 60 s, in the list and in `marker info`. Six markers spread over about 1 s all read `1m4s` or `1m5s`, so their order and gaps (the whole point for a race) cannot be read.
- Workaround: `marker info m-N --json` and read `start` (ms). Or zoom and hope; `zoom push` does take seconds such as `64.2,64.7`.

## The question "what ran in order on this thread over 150 ms, with exact times" needed a script

- Command: `profiler-cli thread markers --session aTarget-null-3 --min-duration 3 --list --limit 0 --json | python3 …` (and the same for `--search name:ContentTaskUtils`) on the same 69 s profile.
- Question: in which order did the content main thread run its Jank, refresh tick, test poll timer and IPC reply between t=64.40 s and 64.56 s.
- The default output would have answered it if it printed `t=64.532s` rather than `t=1m4s` (see above). The JSON `flatMarkers[].start` and `duration` were enough.
