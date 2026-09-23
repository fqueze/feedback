## `thread markers --list` rounds times to whole seconds on long profiles

- Command: `profiler-cli thread markers --session test_storagePermissionsReject.html-1 --search test_storagePermissionsReject --list --limit 0` on a 21-minute resource-usage profile.
- Question: when, to the millisecond, did each group of TEST-PASS markers start, and how long after the iframe src was set did the frame's own messages arrive? The whole test is 2 s long, so every one of its ~750 markers printed as `t=16m54s` or `t=16m55s`.
- Expected: millisecond times (e.g. `t=1014.794s`), or precision adapted to the spread of the listed markers.
- Workaround: `--json`, then a Python script over `flatMarkers[].start`. The script was also needed to collapse the ~750 near-identical messages into runs grouped by a substring of the message (the `(IFRAME = ...)` suffix); a `--group-by` on a regex capture of the message, or a "collapse consecutive runs" option, would have answered it directly.
