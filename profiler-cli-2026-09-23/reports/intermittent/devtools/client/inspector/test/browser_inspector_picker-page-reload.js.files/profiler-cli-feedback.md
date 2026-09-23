## Question: in what order, to the millisecond, did these markers happen within one second?

- Command: `profiler-cli thread markers --list --limit 0 --session S --search "..."` (zoomed to a 40 ms window)
- Expected: a start time precise enough to order markers inside the zoom (e.g. `t=233.1241s`), since the zoom was already narrower than one second.
- Got: every row printed `t=3m53s`. In a 40 ms zoom, that does not show order or gaps: request received, then `MakeFrameRemote`, then `inner-window-destroyed`.
- Workaround: `--json` piped to a Python script printing `start/1000` with 4 decimals. The same for `marker info m-a m-b ... --json`.
- What the output could have shown: pick the time precision from the zoom width (ms when the view is under a few seconds), or add a `--precise-times` flag.

## `profile logs` ignores the zoom and prints wall-clock UTC only

- Command: `profiler-cli zoom push 232.79,233.35 --session S` then `profiler-cli profile logs --limit 0 --session S`
- Expected: only the Log markers in the zoomed range, with profile-relative times like the rest of the tool (`t=233.12s`).
- Got: all 2,750 entries from profile start (the header said `View: ts-zH→ts-zn (560ms)`), stamped `2026-09-21 01:57:59.616908935 UTC`, with no mapping to profile time. The `DocShellAndDOMWindowLeak` window-creation lines could not be lined up with the markers.
- Workaround: `--thread t-N` to cut the volume, then ordering by sequence alone.
