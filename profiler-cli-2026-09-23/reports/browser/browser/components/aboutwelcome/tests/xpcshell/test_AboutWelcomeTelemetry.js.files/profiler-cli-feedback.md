## `zoom push` silently misparses the `17m50s` format that the tool itself prints

- Command: `profiler-cli zoom push 17m50s,19m50s --session <s>` (on a 54-minute resource-usage profile, where every marker time is printed as `t=17m58s`).
- Expected: a zoom to 1070 s–1190 s, or an error saying the format is not accepted.
- Got: `Pushed view range: ts-1 (17s) to ts-2 (19s) (duration: 2s)` — the minutes part was dropped without warning, and the following `thread markers` query ran on the wrong 2 s window.
- Workaround: convert to seconds by hand (`zoom push 1070,1190`), which then echoes `ts-F (17m50s) to ts-h (19m50s)`.
- Cost: one wasted query, and a wrong reading if I had not noticed the `(17s)` echo.

