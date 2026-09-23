# profiler-cli feedback (browser_bookmarks_change_title.js)

## Question: in what order did a dozen events fire inside 50 ms, one minute into the profile?

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session <s>` (zoomed to 78.64,80.5)
- Expected: a time column precise enough to order `popuphiding`, `deactivate`, `activate` and `command`, which are 1 to 8 ms apart.
- Got: every row reads `t=1m19s`. Past one minute the column is rounded to whole seconds, so the order that decides this failure cannot be read from it.
- Workaround: `--json` and a script printing `start / 1000` to 4 decimals (`seq.py` next to this file). I did this for every marker list in the investigation.
- What would have answered it: millisecond precision in the `t=` column whatever the offset (`t=78.6876s` or `t=1m18.688s`), or at least when the zoomed range is shorter than a few seconds.

## Question: which window did this DOM event fire in?

- Command: `profiler-cli marker info m-482 --session <s>` (a `deactivate` DOMEvent whose target is `window`)
- Expected: something that tells two browser windows apart.
- Got: `target: window` only. The `innerWindowID` exists, but only as `rawFields` in `--json` output.
- Workaround: `marker info --json` for each marker, then comparing `rawFields.innerWindowID` by hand.
- What would have answered it: an `innerWindowID` (or window URL) column in `marker info` text output, and in `--list` rows for DOMEvent markers.

## Default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` (the error message suggests this, which helped). This cost one call.

## Review (review-browser_bookmarks_change_title.js): which frames led to this marker?

- Command: `profiler-cli marker stack m-96 --session review-bct-1` (a `NotifyObservers` TextStack marker)
- Expected: the stack starting at the code that sent the notification.
- Got: frames [1] and [2] are two `XUL!mozilla::base_profiler_markers_detail::AddMarkerToBuffer<...TextStackMarker...>` template instantiations, each several hundred characters long, before the first useful frame. This happens on every `NotifyObservers` and `ChromeUtils.importESModule` marker.
- Workaround: `| grep -v AddMarkerToBuffer` on every `marker stack` call.
- What would have answered it: drop the profiler's own marker-capture frames from `marker stack` output, or shorten them to one line.
- The two issues logged above (the EPERM on the default session directory, and `t=` rounded to whole seconds past one minute) cost this review a call and a script too.
