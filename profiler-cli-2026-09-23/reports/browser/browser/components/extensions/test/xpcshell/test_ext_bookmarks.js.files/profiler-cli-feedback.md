## Question: how busy was the machine around t=273.3s of a resource-usage profile?

- Command: `profiler-cli thread markers --session S --search 'name:CPU Use' --list --limit 0` (after `zoom push 272.8,273.9`)
- Expected: each CPU Use marker's CPU percent in the row, or a summary (median/min/max CPU %) over the zoomed range.
- Got: rows with only name, time and duration; the percent is only in `marker info` (one record per marker) or `--json`. `counter list` says "No counters in this profile" and `profile info` says "CPU activity over time: No significant activity" on a profile whose CPU Use markers read 96-100% for the whole test: misleading.
- Workaround: `--json` and a Python script over `flatMarkers[].data.cpuPercent` to get the median over the test's window and the job.
- What would have answered it: the CPU Use percent in the `--list` row label, or `profile info` on a resource-usage profile summarizing machine CPU from those markers.

## Question: which marker does a link's `marker=N` point to? (review, browser-review-test_ext_bookmarks.js)

- Command: `profiler-cli load '<resource-usage URL with marker=39918&range=272800m1100>' --session S`, then trying to find the marker with index 39918 (one of about 13 identical `CPU Use` markers in the range).
- Expected: `load` to print the handle of the URL's selected marker (e.g. "Selected marker: m-52 CPU Use t=272.945s"), or a `marker info --index 39918`.
- Got: only the session status. `thread markers --list --json` rows do not carry `markerIndex`, so a name search does not tell you which row it is.
- Workaround: listed every handle in the zoomed range with `--list --limit 0 --json`, passed all 284 to `marker info --json`, and filtered on `markerIndex` in Python.
- What would have answered it: `load` naming the URL's selected marker, or `markerIndex` in the `--list --json` rows.
