## Marker times past one minute are rounded to whole seconds in text output (browser_ml_utf8_codepoint.js)

- Command: `profiler-cli thread markers --category Test --search browser_ml_utf8_codepoint --list --limit 0 --session <s>`
- Question: how long did each subtest take (start of "Entering test X" to "Leaving test X"), to tell 11.0 s from 11.3 s per engine creation.
- Expected: timestamps with ms precision, as for times under a minute (`t=13.691s`).
- Got: `t=2m26s`, `t=2m37s`, ... — whole seconds only, for every marker past 60 s; `marker info` prints `Time: 2m26s` too.
- Workaround: `--json` and a script over `flatMarkers[].start`. Output could print `t=2m26.066s` (or seconds with ms, `t=146.066s`).
