## Question: in what order did these markers happen (ms resolution on a long profile)

- Command: `profiler-cli thread markers --category Test --search browser_tab_preview.js --list --limit 0 --session badge-null-1` (10m39s profile)
- Expected: start times I can order events 1 ms apart with (the race here is `TabNote:Enabled` at 618.126 vs `popupshowing` at 618.127).
- Got: `t=10m18s` for every row of the subtest; all 15 events of interest print the same time.
- Workaround: `--json` and a 6-line script printing `start/1000` with 3 decimals (`ml.py` in this directory), for every list in this investigation.
- Could have shown: times with ms precision (e.g. `618.127s`), or a `--time-precision` option.

## Question: how much was the machine writing to disk around t

- Command: `profiler-cli profile info` and `profiler-cli counter list` on a `profile_resource-usage.json` (session badge-null-r2)
- Expected: CPU and IO tracks to read over a zoomed range.
- Got: `No significant activity.` and `No counters in this profile.` The data is in `CPU Use` / `IO` interval markers whose values (`write_bytes`, `cpuPercent`) only appear in `marker info`, one record per 100 ms.
- Workaround: `thread markers --search name:IO --list --json`, then `marker info <all handles> --json` from a script to print write_bytes per interval and the job-wide percentiles. Also, `--search name:IO` matched `NetIO` too.
- Could have shown: those markers as counters, or `thread markers --list` printing the payload fields for CPU/IO markers.

## Question: which marker does this link's `marker=N` select (review-browser_tab_preview.js.bug1980036.badge-null)

- Command: `profiler-cli marker info m-533 --session review-badge-null-2 --json` gave `markerIndex` 1499023, but the report's link had `marker=1499021`.
- Expected: a way to look up the marker at index N, e.g. `marker info --index 1499021`.
- Got: no such lookup. `marker info` only takes `m-N` handles.
- Workaround: `zoom push` onto that 1 ms, list its markers, then run `marker info --json` on each handle until one reported 1499021. It was a `notifyResultsOnCallingThread` runnable.
- Could have shown: `markerIndex` in `thread markers --list --json` rows, or a lookup by index.
