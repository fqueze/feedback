## Sub-second times lost in `thread markers --list` past one minute

- Question: in which order did ~40 IPC messages, loads and DOM events happen within a 100 ms window (Enter keydown, RecordEngagement, Load 502, LoadURL, OnLocationChange, SetURI)?
- Command: `profiler-cli thread markers --category Test --search browser_persist_searchMode --list --limit 0 --session <s>` (and the same with `--search "SendQuery,ReceiveQuery,..."` after `zoom push 87.52,87.75`)
- Expected: a start time precise enough to order markers, e.g. `t=87.5373s` (or `1m27.537s`).
- Got: `t=1m27s` / `t=1m28s` on every row, so rows 10 ms apart are indistinguishable; `marker info` also prints `Time: 1m28s - 1m28s (38.929ms)`.
- Workaround: `--json` piped through a Python one-liner printing `start/1000` with 4 decimals. Needed for every listing in this investigation.
