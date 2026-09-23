## Which document does a marker belong to?

Question: "RefreshDriverTick waiting for paint" markers (NoPayloadUserData, only an `innerWindowID`
payload) repeat 431 times in the WebExtensions process; which document (URL) is stuck?

- `profiler-cli thread markers --search "innerWindowID:4294967331" --list --session S` -> 0 markers.
- `profiler-cli thread markers --search "4294967331" --list --session S` -> 0 markers.
- `marker info m-756` shows `innerWindowID: 4294967304` under "raw payload, no schema", but not the
  window's URL.

Expected: searching on the innerWindowID (a payload value the guide says bare terms match), or
`marker info` resolving the innerWindowID to the page URL from the profile's `pages` table.

Workaround: `thread markers --list --limit 0 --json` over a zoom, then a Python script grepping each
marker's `data` for the id; the DocumentLoad marker with that innerWindowID names
`moz-extension://.../popup.html`.

## (review) Which process does each vsync notification go to, and how many?

Question: of the `PVsync::Msg_Notify` messages the parent sends in a range, how many go to each
pid? (The report's claim "506 of 524 go to the WebExtensions process" rests on this.)

- `profiler-cli thread markers --search PVsync --session S` -> only "IPCOut 1087 / IPCIn 325"
  aggregates; `--list` gives one line per message without the other pid.

Expected: `--group-by` on a payload field (`otherPid`, `messageType`) giving counts per
destination, e.g. `--search PVsync::Msg_Notify --group-by data.otherPid`.

Workaround: `--list --limit 0 --json`, then a Python script over `flatMarkers[].data.otherPid`.

## (review) Loading a raw Taskcluster URL selects a different thread than a profiler link

- `profiler-cli load --session S '<profiler.firefox.com link ...&thread=0>'` -> selected t-0.
- `profiler-cli load --session S 'https://firefox-ci-tc.../profile_...json'` (same profile) ->
  selected t-20 (GeckoMain, WebExtensions).

Subsequent `thread markers --search` calls silently ran on the WebExtensions thread and found
the wrong markers; only the header line gave it away. Expected: the parent GeckoMain by default,
as the profiler UI does. Workaround: `thread select t-0` after every load.

Correction to "Which process does each vsync notification go to": it exists —
`thread markers --search PVsync::Msg_Notify --group-by name,field:otherPid` gives 2221: 408,
2224: 2, 2226: 2. The cost was discoverability: the default aggregate output and its "Use
--group-by <keys>" hint never mention `field:<payload key>`, and `--help` only shows
`field:eventType`. Listing the groupable payload keys of the matched markers would have saved
the script.
