## test_listsources-02.js diagnosis (2026-09-22)

### Question: "which tests were running at time T / did many tests start and end together?"
- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json` then a Python script to bucket start/end times and to intersect the tests running at a given instant across 6 profiles.
- Expected: a way to ask for the interval markers that overlap an instant (e.g. `--at 131.6`) and a histogram of marker end times.
- Got: only a flat list of 1,113 test markers; the "482 tests started within 1 s and all ended 45 s later" pattern needed a script.
- Could show: `thread markers --overlapping <t>` and/or an end-time histogram in the aggregate view for interval markers.

### `--json` flatMarkers omit `duration` for instant markers
- Command: `profiler-cli thread markers --search "...,Phase" --list --json`
- Expected: `duration: 0` or `null` on instant markers.
- Got: key missing, so `x['duration']` raised KeyError in a script.
- Workaround: `x.get('duration') or 0`.
