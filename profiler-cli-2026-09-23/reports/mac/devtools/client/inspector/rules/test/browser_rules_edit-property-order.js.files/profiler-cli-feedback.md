# profiler-cli feedback (browser_rules_edit-property-order.js)

## `thread markers --list` time column is rounded to the second, even inside a 2 ms zoom

- Command: `profiler-cli zoom push 207.0588,207.061 --session epo-1` then `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session epo-1`
- Expected: a time column precise enough to order markers inside the zoom (ms or sub-ms, or relative to the zoom start).
- Got: every row reads `t=3m27s`; 35 markers in 2.2 ms are indistinguishable, so the order of the blur, the popup opening and the click could not be read from the list.
- Workaround: `--json` and a script printing `start` for each `flatMarkers[]` entry.
- Question the default output did not answer: "in which order did these events happen within this 2 ms window".

## `load` of a Taskcluster URL timed out at the 60 s default, but kept loading

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/SazzLziUQPeKYcvZaOt8mw/runs/0/artifacts/public/test_info/profile_browser_rules_edit-property-order.js.json --session epo-1`
- Expected: either success or a failure that stopped the daemon.
- Got: `Error: Profile load timeout after 60000ms`, while the daemon kept loading; `status` then answered "Profile still loading", then "Timed out after 30000ms waiting for the daemon", and finally worked about 3 minutes later.
- Workaround: poll `profiler-cli status --session epo-1` until it answers.

## (review) Marker aggregates in a zoom count the full duration of markers that start before it

- Command: `profiler-cli zoom push 207.0453,207.0596 --session review-edit-property-order-1`, then `profiler-cli thread markers --search name:Runnable --session review-edit-property-order-1`
- Expected: a way to see how much of the 14.3 ms zoom the matching markers cover, clipped to the zoom and without counting nested markers twice.
- Got: `76 markers (interval: min=83.000ns, avg=147.19μs, max=4.619ms)`. The only total you can get is count × avg, 11.2 ms, and it includes all 4.6 ms of `m-108`, which starts at 207040.7, before the zoom. The report quoted 11.2 ms. Clipped to the window, the markers actually cover 6.6 ms.
- Workaround: `--list --limit 0 --json`, then a script that clips each marker to the window and merges overlapping intervals.
- Question the default output did not answer: "how busy was this thread with X during this window".
