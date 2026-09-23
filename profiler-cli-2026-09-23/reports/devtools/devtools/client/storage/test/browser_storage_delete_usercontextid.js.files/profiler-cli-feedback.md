# profiler-cli feedback (browser_storage_delete_usercontextid.js)

## Question: which document is this DOMEvent's window?

- Command: `profiler-cli marker info m-475 --session browser_storage_delete_usercontextid.js-1`
- Expected: the event's document, e.g. `resize - window (chrome://devtools/content/storage/index.xhtml)`. The profile's pages table maps innerWindowID to URL.
- Got: `Event Target: window`. The innerWindowID (`422`) only appears under `rawFields` with `--json`, and nothing maps it to a URL.
- Workaround: search for a DOMEvent on an element known to live in the storage panel (`--search storage-tree`), read its innerWindowID with `--json`, and compare. That took three extra commands per profile.

## Question: in what order did these markers happen, within the same millisecond?

- Command: `profiler-cli thread markers --list --search ...` and `profiler-cli marker info m-348 m-172 m-221 ...`
- Expected: enough precision to order markers that share a millisecond, across threads (a timer runnable, the RDP request it starts, an IPC message, a test assertion, all within 1 ms).
- Got: every time printed as `t=41.984s`. The order needed `marker info --json` and a script reading `start`/`end`.
- Could show: sub-millisecond times (e.g. `41.984134s`) when neighbouring rows share a millisecond, or a `--precise` flag on `--list` and `marker info`.
