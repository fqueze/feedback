## Question: at ms resolution, in what order did popupshowing, the refresh ticks and the failing assertion happen?
- Command: `profiler-cli zoom push 206.738,206.826 --session S; profiler-cli thread markers --search "name:DOMEvent,Test,RefreshObserver" --list --limit 0 --session S`
- Expected: each row's start time at a resolution matching the zoomed view (an 88 ms view needs 0.1 ms).
- Got: every row printed as `t=3m27s` (1 s resolution), so the list only gave the order, not the gaps (e.g. "the tick came 4 ms after the assertion", "popupshown 14 ms after popupshowing").
- Workaround: `--json` piped to a 6-line Python script printing `start/1000` with 4 decimals (`timeline.py` in this directory). Showing `t=206.8063s` (or ms-precision relative time) whenever the profile is longer than a minute would have answered it.
## `load` of a 3-minute CI per-test profile times out at 60 s, and chained commands then fail
- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/U42pqyoLQJKC9X-Nrvb5FA/runs/0/artifacts/public/test_info/profile_browser_tab_groups_tabContextMenu.js.json" --session S && profiler-cli thread select t-0 --session S`
- Expected: the load waits until the profile is ready (as the two previous loads of similar profiles did).
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`, then `Profile still loading, try again shortly` from the next commands. The daemon kept loading and was ready about a minute later.
- Workaround: poll `profiler-cli status --session S`, and set `PROFILER_CLI_LOAD_TIMEOUT_MS=400000` for later loads. A longer default for URL loads, or `status` reporting "loading", would help.
## (review) First `load` of a CI per-test profile URL died with SIGABRT; the identical retry worked
- Command: `PROFILER_CLI_SESSION_OWNER=review-browser_tab_groups_tabContextMenu.js PROFILER_CLI_LOAD_TIMEOUT_MS=400000 profiler-cli load "<profiler.firefox.com/from-url link to TsWjpJHbT3SZ0a6kOcE6pA profile_browser_tab_groups_tabContextMenu.js.json>" --session review-browser_tab_groups_tabContextMenu.js-1`
- Expected: the profile loads. Got: `The profiler-cli daemon died while loading the profile (killed by signal SIGABRT)`; the log's last real line was `Fetching profile from ...`, nothing about the cause. 40 GB were free.
- Workaround: ran the same command again; it loaded. Logging why the daemon aborted (heap limit? fetch error?) would say whether to retry or wait.
