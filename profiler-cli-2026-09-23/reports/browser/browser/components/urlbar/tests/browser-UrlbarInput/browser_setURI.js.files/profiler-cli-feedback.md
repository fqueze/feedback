## Question: in what order, and how many ms apart, did the markers inside one 1.5 s test happen?

- Command: `profiler-cli thread markers --session <id> --list --limit 0` after `zoom push m-12` (a 1.485 s test marker), on a resource-usage profile at t≈6m7s.
- Expected: start times to ms precision, so "PASS at 367.186, Tabbrowser.destroy error at 367.199 (13 ms later)" can be read directly.
- Got: every row printed as `t=6m7s`. With 200 markers in the same second, the order and gaps could not be read.
- Workaround: `--json` and a Python script over `flatMarkers[].start`. The default list could print a precision that adapts to the zoom range, e.g. `t=367.186s` when the view is under a few seconds.
