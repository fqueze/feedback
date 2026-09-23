## Session name following the brief's convention is too long for the socket path

- Command: `PROFILER_CLI_SESSION_OWNER=browser-browser_ext_webNavigation_onCreatedNavigationTarget_named_window.js profiler-cli load <taskcluster URL> --session browser-browser_ext_webNavigation_onCreatedNavigationTarget_named_window.js-1`
- Expected: the session loads (the brief asks for `<report name>-1`, with a `browser-` prefix here).
- Got: `The Unix socket path for this session is 110 bytes, over this platform's 107-byte limit`, after the download had started.
- Workaround: a shorter session name (`browser-onCNT_named_window-1`). The check could run before the download, or the socket could be named by a hash of the session id so long test names fit.

## (review) Which process each IPC message went to, by search

- Command: `profiler-cli thread markers --session browser-review-webnav-named-1 --search "sent to WebExtensions"` (parent main thread, zoomed to one 8 s wait)
- Expected: the 484 IPC markers whose `niceDirection` is `sent to WebExtensions (Thread ID: 36093)`. The guide says a bare term matches all payload values.
- Got: 0 markers. `--group-by field:niceDirection` on `--search PVsync` shows that value on 484 markers. The same happens with `--search innerWindowID:4294967338` on the WebExtensions thread: 0 markers, though `--group-by field:innerWindowID` groups 22,691 markers under that value (it is in `rawFields`).
- Workaround: `--group-by field:niceDirection` / `field:innerWindowID`, and `marker info --json` to read `rawFields`. Search could cover derived and raw fields too, or say which fields it skipped.

## (review) Links from a session loaded from a profiler link

- Command: `profiler-cli load 'https://profiler.firefox.com/from-url/…/marker-table/?marker=1161741&thread=0&v=17' --session browser-review-webnav-named-4`, then `profile-link.py --session browser-review-webnav-named-4 --marker m-97`
- Expected: a link (the review brief says to check a report's links by loading them).
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself`. A third load of the same 0.6 GB profile was needed just to add two links.
- Workaround: reload from the Taskcluster URL. `profile-link.py` could take the artifact URL from the `from-url` part of the link.
