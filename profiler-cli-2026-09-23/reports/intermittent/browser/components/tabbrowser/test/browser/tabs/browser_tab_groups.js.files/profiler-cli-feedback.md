## Ordering markers a few ms apart in a long profile (browser_tab_groups.js)

- Command: `profiler-cli thread markers --session S --category Test --search browser_tab_groups --list --limit 0`, and `marker info m-N`
- Expected: timestamps precise enough to order events within one refresh tick.
- Got: `t=3m19s` for every marker (profile is 3m23s long) in both the list and `marker info` ("Time: 3m19s (instant)"), so ~20 markers of interest all read the same time.
- Workaround: `--json` and print `start` myself, for every list I read. Question to log: "the ms-level order of markers within one second". Printing seconds with 3 decimals (as `zoom push` accepts) would answer it.

## zoom push with a range in ms instead of s

- Command: `profiler-cli zoom push 199180,199240 --session S` (meant ms; the profile is 203 s long)
- Expected: an error, since the range starts far past the end of the profile.
- Got: accepted, view `ts>10x→ts>10X (1m)`, then `thread markers` said "0 markers in view" as if the window were empty.
- Workaround: re-zoom in seconds. A range outside the profile should be rejected.

## Which JS code dirtied style, per SetNeedStyleFlush marker, in a window

- Command: `thread markers --search SetNeedStyleFlush --list` then `marker info <113 handles>` and a Python script to keep the first JS frames of each stack.
- Question: "which JS function caused each SetNeedStyleFlush in this 60 ms window". The list shows only "SetNeedStyleFlush ✓"; showing the innermost JS frame of the stack in `--list` would have answered it without the script.

## load hangs in "Fetching profile" with no error (review-browser_tab_groups.js)

- Command: `profiler-cli load "https://profiler.firefox.com/from-url/...N-dm2EFGTnCeUjNXvnAzyw...profile_browser_tab_groups.js.json/..." --session S`, then again with `PROFILER_CLI_LOAD_TIMEOUT_MS=280000`.
- Expected: the profile loaded, or a fetch error.
- Got: twice, the daemon log stops at "Fetching profile from https://firefox-ci-tc..." until the client gives up ("Profile load timeout after 280000ms"). `curl` fetched the same 110 MB artifact in 5 s at that moment; the third `load` worked in about a minute.
- Workaround: stop the session and load again. A fetch timeout or progress in the daemon, and a retry, would have saved ~6 minutes.

## profile-link.py refuses a session loaded from a profiler link (review-browser_tab_groups.js)

- Command: `python3 profile-link.py --session S --marker m-13`, on a session loaded with `profiler-cli load <profiler.firefox.com/from-url link>` as review-brief.md says.
- Expected: a link to the marker.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link."
- Workaround: load the same profile a second time from its Taskcluster URL to link new observations. Either the script could take the artifact URL from the from-url link, or the review brief should say to load the Taskcluster URL and push the link's range.
