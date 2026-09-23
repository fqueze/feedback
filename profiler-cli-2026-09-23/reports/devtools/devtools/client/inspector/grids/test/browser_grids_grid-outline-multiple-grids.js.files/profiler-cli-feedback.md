## `thread samples` / `thread functions` after `zoom pop` / `zoom clear` count a stale range

- Command: on a loaded CI per-test profile (task FShaPjD8RQ2Hwo5CV33vHQ, profile_browser_grids_grid-outline-multiple-grids.js.json), thread t-0:
  `profiler-cli zoom push 4.33,4.9 --session S; profiler-cli thread samples ...; profiler-cli zoom pop --session S; profiler-cli thread samples --session S --include-idle`
- Expected: header `View: Full profile` and all 477 samples of the thread (`thread info` says 477; `zoom push 0,4.9` gives 477 running samples).
- Got: header says `View: Full profile`, but the categories say `47 running samples` — the count of the range just popped. After an earlier `zoom clear`, `thread functions --search grid-inspector` in "Full profile" reported 14 samples in total, so a search over the whole profile silently searched only a sub-range.
- Cost: I first concluded the profile had almost no JS samples and that the grid-inspector functions were never sampled.
- Workaround: `zoom push 0,<end>` explicitly instead of relying on the full view after a pop/clear.

## Marker times past 60 s print as `1m1s`, losing all sub-second precision

- Command: `profiler-cli thread markers --search "Toggling ON,TEST-UNEXPECTED" --list` and `profiler-cli marker info m-27 m-11` on task cFYMIbqsTB6nQuLO-69v4A's per-test profile (a 62 s profile).
- Expected: `t=60.848s` and `t=61.145s`, as the list prints for markers under a minute (`t=4.178s`).
- Got: `t=1m1s` and `Time: 1m1s (instant)` for both — three markers 300 ms apart look simultaneous, and the value cannot be fed to `zoom push`.
- Workaround: `marker info --json` and read `start` (ms).

## (review) Ordering a race past 60 s still needs a script; list and info JSON use different keys

- Question: "exact ms order of the `_updateGridPanel` dispatch vs the `getHighlighterByType` response" on task cFYMIbqsTB6nQuLO-69v4A (markers at t≈60.9 s, 1 ms apart).
- Command: `profiler-cli thread markers --search "SetNeedStyleFlush,RDP,..." --list --limit 0 --json`, then a Python script over it.
- Cost: the default list prints `1m1s` for every row (as logged above), so I had to use a script. In the script, I first read `.markers` (the key `marker info --json` uses), but `thread markers --json` puts rows under `.flatMarkers`. That cost one failed run.
- Could have shown: `t=60.897s`, and the end time for interval markers, in the default `--list` output.
