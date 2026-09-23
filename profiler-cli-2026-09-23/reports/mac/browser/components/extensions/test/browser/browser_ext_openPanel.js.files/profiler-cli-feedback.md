## Question: in what order, to the millisecond, did these markers happen? (browser_ext_openPanel.js)

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session browser_ext_openPanel.js-1` (thread t-41, zoomed to a 45 s window)
- Expected: a time column precise enough to order events a few ms apart (the diagnosis hinged on mousedown at 136.847 s vs PBrowser::Msg_UpdateDimensions at 136.849 s).
- Got: every row printed `t=2m17s`: the column has one-second resolution once the profile is past a minute, even inside a zoom, so rows from two threads cannot be interleaved.
- Workaround: `--json` and a python one-liner printing `flatMarkers[].start` with 3 decimals, run once per thread. Output could show ms (e.g. `t=136.847s`, or relative to the zoom start) when the view is short.

## `zoom push` accepts a range entirely outside the profile

- Command: `profiler-cli zoom push 136810,136900 --session browser_ext_openPanel.js-1` (ms values copied from `--json` `start`, which is in ms, while `zoom push` takes seconds)
- Expected: an error, since 136810 s is far past the profile's 3m2s end.
- Got: a silent zoom to `ts>10J→ts>10k (1m30s)`; the next commands reported "0 markers in view" as if the thread had nothing.
- Workaround: noticed the view label, `zoom pop`, used seconds. Refusing (or warning on) a range past the profile end would catch the ms/s mix-up that `--json` invites.

## Question: when, in profile time, was this IPC message sent? (review of browser_ext_openPanel.js)

- Command: `profiler-cli thread markers --list --limit 0 --json --session review-openPanel-1` (thread t-0, zoomed to 136.815–136.87 s), reading IPC markers
- Expected: the IPC payload's `data.startTime` / `sendStartTime` / `recvEndTime` on the same time base as the marker's `start`, so they could be compared with the other markers.
- Got: every IPC payload time is 7.437 ms later than `start` (for example m-245: `start` 136815.60, `data.startTime` 136823.04). That is the profile's `rootRange.start`: `start` is rebased, the payload is raw. Read naively, an IPC message looks as if it happened after events that in fact came after it.
- Workaround: used `start` only, and confirmed the offset from `rootRange` in `marker info --json`. The output could rebase the payload timestamps as well, or label them as raw.
