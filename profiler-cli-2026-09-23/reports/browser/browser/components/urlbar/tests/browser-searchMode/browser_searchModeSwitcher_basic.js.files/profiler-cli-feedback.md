## `thread markers --list` rounds times to whole seconds past 1 minute

- Command: `profiler-cli thread markers --category Test --search browser_searchModeSwitcher_basic.js --list --limit 0 --session ...`
- Expected: millisecond timestamps (e.g. `t=122.618s`), since the list is the test's log and ordering/intervals within one second matter.
- Got: `t=2m3s` for every marker in that second; the INFO "Do action" and the TEST-UNEXPECTED-FAIL 110 ms apart print the same time.
- Workaround: `marker info <handles> --json` and read `start`.

## Precise marker times needed a script over `--json`

- Question: "in what order, to the millisecond, did these DOMEvent / TimeoutExecutor / RefreshDriverTick markers happen" (a race between two setTimeouts and a refresh tick 5 ms apart).
- Command: `profiler-cli thread markers --search "TimeoutExecutor,RefreshDriverTick,DOMEvent" --list --limit 0 --json --session ... | python3 -c '...print(start)...'`
- The default `--list` output could have shown `t=122.4614s` instead of `t=2m2s`, even when zoomed to a 20 ms range.


## (review) Loading the raw Taskcluster URL selects a content thread, not the parent main thread

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Dixf9h_yTY-RavvTdxd_Yg/runs/0/artifacts/public/test_info/profile_browser_searchModeSwitcher_basic.js.json' --session ...`, then `zoom push 96.0705,96.0795` and `thread markers --search DoFlushPendingNotifications --list`
- Expected: the parent process GeckoMain (t-0, the highest-CPU thread, and where the test's log lives), as loading the profiler link with `thread=0` gives.
- Got: `t-47 (GeckoMain, Privileged Content)` selected, and the search printed "No markers match the specified filters." — which reads as the marker being absent. The selected thread is only shown in the header line, easy to miss after a load.
- Workaround: `thread select t-0`. Needed because `profile-link.py` refuses a session loaded from a profiler link, so a reviewer who checked links that way must reload the raw URL to add one.
