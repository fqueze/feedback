## Question: "was the machine's CPU / iowait / disk write rate high during this test?" (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use,name:IO" --list --limit 0 --session <resource profile>`
- Expected: each row showing its values (cpuPercent, iowait_pct, write_bytes), or `counter list`/`counter info` exposing them as time series with the per-bucket "over time" view.
- Got: `counter list` says "No counters in this profile."; the `--list` rows for `CPU Use` / `IO` / `NetIO` have an empty description, so the values need `marker info` per marker (one per 100 ms) or `--json` plus a script.
- Workaround: `--json` and a Python script over `flatMarkers[].data` (`iowait_pct`, `write_bytes`). This is what showed 168 MB/s of steady disk writes with 20-50% iowait over 15 s.
- What would have answered it: the list row description of `CPU Use` showing e.g. `cpu 35.7% iowait 13.9%`, and of `IO` showing `written 16.5MB (63 writes)`; or `counter info`-style buckets over a zoom range for these marker types.

## `counter info` on a Process CPU counter gives contradictory values zoomed vs unzoomed

- Profile: https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/bkqQJ3NQQmSsWB236h8lzw/runs/0/artifacts/public/test_info/profile_browser_createLogin.js.json, counter c-2 (Process CPU of p-1 Utility Process, whose only thread has 8 ms of CPU in the whole profile).
- Command: `profiler-cli zoom push 5.079,5.405` then `profiler-cli counter info c-2`
- Expected: about 0%, as the unzoomed `counter info c-2` shows 0.2% for the 3.929s-5.238s and 5.238s-6.548s buckets.
- Got: every bucket in the zoomed range between 47% and 68%.
- Cost: sent me looking for a busy utility process that does not exist. Workaround: trust only the unzoomed buckets and the thread's samples.

## Question: "which IPC messages did the parent send/receive in this window?" (review, browser-review-browser_createLogin.js)

- Profile: https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Mh63fqzDTS2-IOx4gut-Fg/runs/0/artifacts/public/test_info/profile_browser_createLogin.js.json, t-0.
- Command: `profiler-cli zoom push 14.19,14.70` then `profiler-cli thread markers --search PBackgroundIDB --list --limit 0`
- Expected: each `IPCOut`/`IPCIn` row showing its message type, e.g. `PBackgroundIDBTransaction::Msg_PBackgroundIDBRequestConstructor`. The search matches on that field.
- Got: IPC rows have an empty description, so you cannot tell a transaction constructor from a `__delete__` without `marker info` on each one, or `--json` plus a script over `data.messageType`.
- Also: every zoomed list starts with about 30 `PBackgroundIDBRequest::Msg___delete__` IPC markers that begin at 1.839s and last 12-18 s. They look mis-paired (seqno reuse on a same-process PBackground channel?). They show up in every window and push the real rows down. The matching `Runnable PBackgroundIDBRequest::Msg___delete__` rows have the right times.
- Workaround: `--json` and a script that prints `data.messageType` and drops `IPCIn`.
