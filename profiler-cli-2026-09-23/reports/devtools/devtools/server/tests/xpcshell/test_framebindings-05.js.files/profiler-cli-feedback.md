# profiler-cli feedback (test_framebindings-05.js)

## Question: "how many seconds after the first launch failure did this test start?" (and "did any test that started after time T pass?")

- Command: `profiler-cli thread markers --search test_framebindings-05.js --list --limit 0 --session test_framebindings-05.js-3`
- Expected: start times precise enough to subtract (ms), as they are below 1 minute (`t=46.519s`).
- Got: past one minute the list shows `t=2m13s` / `t=2m11s`, so the 1.5 s gap between the break (`2m12s`) and the test (`2m13s`) could not be read, nor whether PASS markers at `t=2m11s` started before or after the break at 131.676 s.
- Workaround: `--json` and a Python one-liner over `flatMarkers[].start`; `marker info --json` for single markers.
- What would have answered it: keep millisecond precision in `--list` at any time offset (e.g. `t=2m13.171s`), and/or a `--start-after <t>` filter (zoom includes markers that merely overlap the range, so `zoom push T,end` + `--search status:PASS` also lists tests that started before T).
