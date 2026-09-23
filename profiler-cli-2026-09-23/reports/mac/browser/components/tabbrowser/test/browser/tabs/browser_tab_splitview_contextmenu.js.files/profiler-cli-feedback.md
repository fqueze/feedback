## Default session directory is not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_tab_splitview_contextmenu.js profiler-cli load <taskcluster url> --session browser_tab_splitview_contextmenu.js-1`
- Expected: the session loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command. Defaulting to `$TMPDIR/profiler-cli` when the home directory is not writable would avoid it.

## Question: in what order did these events happen, and how far apart?

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session bsvcm-1` inside a 110 ms zoom.
- Expected: timestamps precise enough to order events within the zoomed range (mousedown, TabSelect, reflow, mouseup were 1-10 ms apart).
- Got: every row printed `t=11m10s`, so the list could not say which came first or by how much. Several interval markers also showed a duration in the text view but `end` was missing from `--json` (duration 0).
- Workaround: `--json` and a Python one-liner printing `start` in ms. The text view could print milliseconds when the view range is under a few seconds.

## Question: which of these style invalidations came from function X?

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list` then one `profiler-cli marker stack m-N` per handle in a shell loop (about 40 calls) to find the one whose stack held `updateEmptyToolbarMessage`.
- Expected: a way to filter markers by a frame in their stack, e.g. `--search stack:updateEmptyToolbarMessage`, or `--list` showing the top JS frame of each marker's stack.
- Workaround: loop over `marker stack`, grep the first frames.

## Question: what did this part of the window look like?

- Command: `profiler-cli screenshots --range 669.40,669.70 -o <dir> --session bsvcm-1`.
- Expected: enough resolution to tell whether a vertical tab row was drawn multiselected.
- Got: 350x259 thumbnails of a 1280x949 window; the tab strip was about 70 px wide. No crop/zoom option, and `sips --cropOffset` is ignored on this macOS, so I could only upscale the whole frame.
- Workaround: `sips -z` to upscale the whole image and read it by eye. A `--crop x,y,w,h` (in window CSS pixels) with upscaling would help.

## `marker info a..b --json` records have no handle

- Command: `profiler-cli marker info m-429..m-436 --json --session bsvcm-1`
- Expected: each record to carry its `handle`, like `thread markers --json` rows do.
- Got: `handle` absent (printed `None`), so records had to be matched to handles by position.


## Loading the raw Taskcluster URL selects a content thread (review)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/LkI0GXVTRmS2v7UCUIE8bw/runs/0/artifacts/public/test_info/profile_browser_tab_splitview_contextmenu.js.json --session rv-bsvcm-3b`, then `thread markers --search SetNeedStyleFlush --list`.
- Expected: the parent process main thread selected, as when loading the profiler link with `thread=0`.
- Got: `t-217 (GeckoMain, Privileged Content)` selected; the marker query returned nothing, with no hint that the thread was the wrong one.
- Workaround: `thread select t-0`. Defaulting to the parent process GeckoMain, or naming the selected thread above an empty result, would avoid it.
