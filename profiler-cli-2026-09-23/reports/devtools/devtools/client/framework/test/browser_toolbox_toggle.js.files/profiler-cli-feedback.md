## Which document is innerWindowID N?

- Command: `profiler-cli marker info m-497 m-559 m-558 --session browser_toolbox_toggle.js-1` (DOMEvent focus/activate markers), then `profiler-cli profile info --json`, `profiler-cli profile meta`.
- Expected: the URL of the page behind a DOMEvent marker's `innerWindowID` (the profile's `pages` table has it), e.g. `innerWindowID: 2412 (chrome://devtools/content/framework/toolbox.xhtml)`.
- Got: only `innerWindowID: 2412`; no command maps an innerWindowID to its URL.
- Workaround: inferred each document from other markers' targets in the same window (`div class="toolbox-tabs"` for toolbox.xhtml, `browser class="devtools-toolbox-iframe window-host"` for the host window). Focus-race diagnoses hinge on which document got `focus`, so this costs a few rounds every time.

## Screenshots of a secondary window are black

- Command: `profiler-cli screenshots --at 240 -o shots-1 --session browser_toolbox_toggle.js-1`
- Expected: an image of the 900x320 devtools host window.
- Got: `screenshot-m-70.jpg` (win 48, 900x320) is an all-black image. May be the recording, not the tool; saying so in the output (e.g. "image is uniform black") would save opening it.

## Which markers belong to window N? (review-browser_toolbox_toggle.js)

- Command: `profiler-cli thread markers --session review-browser_toolbox_toggle.js-2 --search "innerWindowID:2378" --list` (also bare `--search 2378`)
- Expected: the DOMEvent markers whose `innerWindowID` is 2378, so their element targets say which document it is.
- Got: `No markers match`. `innerWindowID` shows up only under `rawFields` in `marker info --json` (as `data.innerWindowID` in `thread markers --json`), and `--search` does not match it.
- Workaround: `thread markers --search DOMEvent --list --limit 0 --json` plus a Python filter on `data.innerWindowID`.
