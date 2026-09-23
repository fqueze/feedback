## `thread markers --list` rounds times to whole seconds in a long profile

- Command: `profiler-cli thread markers --session browser_ext_webNavigation_getFrames.js-1 --category Test --search webNavigation_getFrames --list --limit 0`
- Expected: ms-resolution start times, to order the test's steps and measure the vsync wait.
- Got: every row of a 6m16s profile reads `t=6m7s` or `t=6m16s`; `marker info` prints `Time: 6m7s` too.
- Workaround: `--json` and `flatMarkers[].start`.

## Default selected thread after `load` is not the parent main thread

- Command: `profiler-cli load <Linux profile_browser_ext_webNavigation_getFrames.js.json URL> --session browser_ext_webNavigation_getFrames.js-2`, then `thread markers --category Test --search webNavigation_getFrames --list --limit 0`
- Expected: the parent process GeckoMain selected (as with the macOS profile of the same test), or a note that another thread is selected.
- Got: t-62 (GeckoMain, WebExtensions) selected; the search answered "No markers match", which reads like "the test logged nothing".
- Workaround: `thread select t-0`.

## Question: which document's refresh driver keeps vsync on during a range?

- Command: `thread markers --search "waiting for paint" --list --json --limit 0` on the WebExtensions main thread, then a script counting `data.innerWindowID` inside the wait's range and mapping it to the `initial timer start <url>` marker.
- Expected: a grouping of `RefreshDriverTick` markers by innerWindowID (with the document URL) in the text output.
- Workaround: the script. (Same question as the frameId0 report's feedback.)

## Review note (review-browser_ext_webNavigation_getFrames.js): the innerWindowID grouping exists

- Command: `profiler-cli thread markers --session review-browser_ext_webNavigation_getFrames.js-4 --thread t-29 --search "waiting for paint" --group-by "field:innerWindowID"`
- Got: `4294967338: 20687 markers`, plus the 3 others, with no script needed. The counts per window were there; what the text output still lacks is the window's document URL (from its `initial timer start`), and the guide does not mention that `--group-by field:<rawField>` works on raw fields such as `innerWindowID`.
