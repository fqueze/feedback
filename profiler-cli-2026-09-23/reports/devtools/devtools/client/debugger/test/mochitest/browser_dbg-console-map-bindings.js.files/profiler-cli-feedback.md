## "In what order, and how far apart, did these markers happen within one second?"

- Command: `profiler-cli thread markers --category Network --list --limit 0 --session <s>` (and every other `--list`), zoomed on a 600 ms window.
- Expected: timestamps precise enough to order markers and measure gaps (ms), at least when the view is under a few seconds.
- Got: every row reads `t=6m28s`; the ~700 rows of a 640 ms window cannot be ordered or measured against each other.
- Workaround: `--list --json` plus a python script printing `start`, then `marker info --json` for exact times.

## "Did this request fail, with which status, and when did it start relative to the previous network request?"

- Command: `profiler-cli thread network --sort start --limit 0 --session <s>` (zoomed 357 s..388.3 s).
- Expected: each request's start time (and requestStart/responseEnd), and its `requestStatus` when not NS_OK.
- Got: no timestamps at all, and the failed request's status shown as `???` (its payload has `requestStatus: NS_ERROR_ABORT`).
- Workaround: `thread markers --search "name:Load " --list --json`, then `marker info <handles> --json` and a script printing start, cache, requestStatus, httpVersion, requestStart, responseEnd. This was the key evidence (a 30.04 s gap since the last HTTP/3 response, matching `network.http.http3.idle_timeout`).
