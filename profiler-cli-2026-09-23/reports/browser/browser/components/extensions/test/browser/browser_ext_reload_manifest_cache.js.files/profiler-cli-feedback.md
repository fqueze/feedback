## Which markers belong to one window (innerWindowID)
- Command: `profiler-cli thread markers --search 4294967338 --list` and `--search innerWindowID:4294967338 --list` on t-21 (WebExtensions) of the QpSrFmkpR1Gt5mBA-g7xLw per-test profile.
- Expected: the 11,630 `RefreshDriverTick waiting for paint` markers whose payload is `{innerWindowID: 4294967338}` (shown by `marker info` under "Fields (raw payload, no schema)"), plus that window's DocumentLoad/DOMEvent markers.
- Got: "No markers match the specified filters."
- Workaround: `--list --limit 0 --json` piped to python, filtering `data.innerWindowID`. (`--group-by field:innerWindowID` did work.)

## Which document is innerWindowID N
- Question: what page is innerWindowID 4294967338? The profile's pages table has the URL.
- Got: no command that says; had to scan DocumentLoad markers in `--json` for that id (found `moz-extension://…/popup.html`).
- Could show: the page URL next to innerWindowID in `marker info`, and in `--group-by field:innerWindowID` rows.

## When a marker series starts and ends
- Question: from when to when did one window's `RefreshDriverTick waiting for paint` run, and with what gaps?
- Command: `thread markers --search "waiting for paint" --group-by name,field:innerWindowID`.
- Got: counts per window, but no first/last time; `--list` rows print `t=3m24s` (1 s resolution past a minute), too coarse to zoom around an instant marker.
- Workaround: `--json`, `flatMarkers[].start`. Group rows could show first/last start; `--list` could keep ms precision past 1 min.

## (review) Linking from a session loaded the way the review brief says
- Command: `profiler-cli load '<profiler.firefox.com/from-url/… link from the report>' --session …`, as `review-brief.md` says, then `profile-link.py --session … --marker m-22486`.
- Expected: a link to the new marker.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself". I had to load the same 0.6 GB profile a second time to link two new observations.
- Could do: `profiler-cli` could record the underlying `from-url` artifact URL when it loads a profiler link, or `profile-link.py` could extract it from the link.

## (review) `--group-by name,innerWindowID` groups everything under "(no value)"
- Command: `thread markers --search "RefreshDriverTick waiting for paint" --group-by "name,innerWindowID"` on t-21.
- Expected: one row per innerWindowID, or an error saying the key must be `field:innerWindowID`.
- Got: every marker (11,633) in a single "(no value)" group. That reads as "these markers have no window", which is false. `field:innerWindowID` works.
