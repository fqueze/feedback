# profiler-cli feedback (browser_tab_preview.js.time-limit)

## Question: in what order, to the millisecond, did markers happen inside one failing second?

- Command: `profiler-cli zoom push 219.9,221 --session S; profiler-cli thread markers --search "name:DOMEvent,..." --list --limit 0 --session S`
- Expected: timestamps precise enough to order markers within the zoomed 1.1 s (the race here is 1 ms wide: test hit-test at 219.9637, refresh tick at 219.9645).
- Got: every row printed `t=3m40s`, even with a 25 ms zoom.
- Workaround: `--json | jq '.flatMarkers[] | .start/1000'` on every listing.
- What the output could show: a time format whose precision follows the zoom width (or ms offsets relative to the zoom start).

## Question: when was each screenshot taken?

- Command: `profiler-cli screenshots --range 219.90,220.2 -o dir --session S`
- Expected: one timestamp per frame precise enough to tell frames 16 ms apart.
- Got: all 7 frames listed as `t=3m40s`.
- Workaround: `marker info <m> --json | jq .start` for each handle.

## Question: which window's refresh driver ticked, and which window got this event?

- Command: `profiler-cli marker info m-307 --json --session S`
- Expected: `innerWindowID` in the JSON, since the text output prints it under "Other payload fields".
- Got: `"data": null` in JSON; the text output did show `innerWindowID: 4`.
- Workaround: grep the text output of `marker info`.
- Why it mattered: two refresh drivers ticked on the parent main thread; only the innerWindowID told the browser window's tick (the one that ran the pending theme change) from another window's catch-up tick.

## (review) Question: is my profile loaded yet?

- Command: `profiler-cli load '<profiler.firefox.com from-url link to a ~250 s CI per-test profile>' --session S`
- Expected: the load to wait until the profile is ready, or to say it is still loading in the background.
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`. The daemon kept loading anyway: `status` then said `Profile still loading, try again shortly`, and a second `load` with a longer timeout failed with `Session S is already running`.
- Workaround: poll `status --session S` until it stops saying "still loading".
- What the output could show: "still loading in session S; poll `status`" in place of an error, or a default timeout long enough for a CI per-test profile.

## (review) Question: what is the label of this marker?

- Command: `profiler-cli marker info m-9740 --json --session S`
- Got: `"label": null, "data": null`, although `thread markers --list --json` has both for the same marker (`data.innerWindowID` included). So the `data: null` entry above still holds for `marker info --json`, and `label` is null there too.
- Workaround: use the `--list --json` rows, or the text output of `marker info`.
