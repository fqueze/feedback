## How many times did one failure mode happen, when its message embeds a per-run UUID?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_incognito_views.js --limit 0`
- Expected: "Cannot show popup for an inactive window ... testPopupForWindow@moz-extension://<uuid>/..." grouped as one issue with its count (169).
- Got: 169 separate `1x` issues ("↑ same as 3, but ...") because each run's moz-extension:// UUID differs; the top of the list shows 363x, 38x, then 1x rows, so the second most frequent mode (169 of 570) looks negligible. `--issue <n>` then yields one task per issue.
- Workaround: strip `moz-extension://[^ ]*` from `--limit 0` output and `uniq -c`; probed `--issue 165..171` to find recent task IDs.
- Could show: normalise moz-extension:// (and similar) UUIDs before grouping, so the count and `--issue` task list cover the whole mode.
