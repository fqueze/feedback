## `field:value` search does not match fields of schema-less payloads, though `--group-by field:` does

- Command: `profiler-cli thread markers --session S --search "innerWindowID:4294967331" --list --limit 12` (thread t-35, WebExtensions GeckoMain; markers `RefreshDriverTick waiting for paint`, "Type: NoPayloadUserData ... Fields (raw payload, no schema): innerWindowID")
- Expected: the 18,849 markers of that window, in order, to see when it started ticking.
- Got: "No markers match the specified filters", while `--group-by field:innerWindowID` on the same markers lists 4294967331 with 18,849 markers.
- Workaround: took the example handles from the group-by output and ran `marker info` on each to get their times.

## (review) profile-link.py refuses a session loaded from a profiler.firefox.com link, which is how review-brief.md says to load

- Command: `profiler-cli load '<report's profiler.firefox.com/from-url/... link>' --session S`, then `profile-link.py --session S --thread t-35 --range 334,338.74 --panel marker-chart --search PVsync`
- Expected: a link, since the from-url link wraps the same Taskcluster artifact URL.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link."
- Workaround: stopped both sessions and reloaded both profiles from the raw Taskcluster URLs, which took two extra loads of about 0.6 GB each. Either profile-link.py should unwrap `from-url/`, or review-brief.md should say to load the raw URL.

## (review) Question: "when did each window's refresh driver start and stop ticking?"

- Command: `profiler-cli thread markers --session S --thread t-35 --search 'RefreshDriverTick waiting for paint' --group-by field:innerWindowID`
- Expected: each group's first and last time, as well as its count.
- Got: only counts and example handles.
- Workaround: `--list --limit 0 --json` (6 MB) and a Python script over `flatMarkers[].data.innerWindowID`.
