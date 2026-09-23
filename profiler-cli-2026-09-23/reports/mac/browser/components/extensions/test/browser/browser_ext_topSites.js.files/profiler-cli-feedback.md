## review-browser_ext_topSites.js: "how many of these markers belong to one window (innerWindowID)?"

- Command: `profiler-cli thread markers --session review-browser_ext_topSites.js-1 --thread t-26 --search "innerWindowID:4294967338"`, and `... --search "waiting for paint" --group-by innerWindowID`
- Expected: the `RefreshDriverTick waiting for paint` markers of window 4294967338 (17,593), or a count per innerWindowID.
- Got: `No markers match the specified filters`, and `(no value): 17595 markers` for the group-by. `marker info --json` shows `innerWindowID` under `rawFields`, not `fields`, so neither `field:value` nor `--group-by` sees it, although the text output prints no hint of this.
- Workaround: `thread markers --search name:RefreshDriverTick --list --limit 0 --json`, then a script counting `flatMarkers[].data.innerWindowID`.
- What would have answered it: `--search innerWindowID:<id>` and `--group-by innerWindowID` working on that key, since which document a Graphics/DOM marker belongs to is the usual question.

## review-browser_ext_topSites.js: `profile markers` rejects `--list`

- Command: `profiler-cli profile markers --search RefreshDriverTick --session review-browser_ext_topSites.js-1 --list --limit 0`
- Expected: the same flag as `thread markers`.
- Got: `error: unknown option '--list'`; `profile markers` already lists, so dropping it worked. One wasted call.
