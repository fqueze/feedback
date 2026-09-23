## `zoom push` accepts the displayed `23m18s` format but zooms to 1 ns

- Command: `profiler-cli zoom push 23m18s,23m24s --session browser_console_webconsole_ctrlw_close_tab.js-1`
- Expected: a zoom to 1398s..1404s (the marker list prints times as `t=23m22s`, so that is the natural thing to paste), or an error.
- Got: no error; `View: ts-1→ts-2 (1.000ns)` near the start of the profile, and the following `thread markers --list` silently showed 5 unrelated markers.
- Workaround: convert to seconds by hand (`zoom push 1398,1404`).

## (review) Question: is marker handle m-N the marker a link's `marker=<index>` points at?

- Command: `profiler-cli marker info m-1 --session review-browser_console_webconsole_ctrlw_close_tab.js-1`
- Expected: the marker's index in the thread's marker table (what `marker=75061` in a profiler URL refers to) somewhere in the default output.
- Got: name, type, time, fields — no index. Only `marker info m-1 --json` has `markerIndex`, which needed a script to pull out for several markers.
- Workaround: `marker info m-1 m-25 --json | python3 -c …` to print `markerIndex` per handle.
