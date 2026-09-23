## `thread markers --list` rounds times to whole seconds (browser_net_view-source-requests.js)
- Command: `profiler-cli thread markers --category Test --search browser_net_view-source-requests.js --list --limit 0 --session browser_net_view-source-requests.js-1`
- Question: how far apart are the test's log lines (tab added, panel shown, each network event), and how do they interleave with network/DocumentLoad markers on other threads?
- Expected: millisecond timestamps, as the whole sequence spans ~450 ms.
- Got: every row `t=3m45s`, so ordering and gaps were unreadable. Same with `profile markers --search view-source` across threads.
- Workaround: `--json` piped to a python script printing `start`. A `--precise`/ms column (or ms when rows share a second) would have answered it.

## `thread network` counts START-only markers as "in flight" for the whole window
- Command: `profiler-cli thread network --sort start --limit 0` (zoomed to 224.6,226)
- Expected: requests active in the zoomed 1.4 s window.
- Got: 48 of 50 rows were `sjs_content-type-test-server.sjs?sts=304` markers from an earlier test at t=2m31s with `status: STATUS_START` and no end marker, reported as "in flight 100%, peak 49 concurrent". Only the 2 real requests mattered.
- Workaround: `rg -v` on the URL. Flagging START-only markers as "no end marker recorded" rather than in flight, or an option to hide them, would help.
