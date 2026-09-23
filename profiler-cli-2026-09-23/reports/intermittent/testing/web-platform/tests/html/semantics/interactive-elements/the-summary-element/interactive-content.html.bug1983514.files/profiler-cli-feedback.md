## Marker list timestamps are second-resolution even inside a short zoom

- Command: `profiler-cli zoom push m-1` (a 2.776 s test marker in a resource-usage profile), then `profiler-cli thread markers --list --limit 0`
- Expected: timestamps precise enough to order and time the ~40 wptrunner DEBUG markers inside the zoom ("Clicking element", "Action click completed").
- Got: every row printed as `t=11m52s` / `t=11m53s`, so the per-click durations (70 ms vs 600 ms) could not be read.
- Workaround: `--json` and a script computing `start - test.start` in ms.
- Question: "how long did each step of this test take, relative to the test start". Relative-to-zoom-start ms timestamps (or ms precision when the view is under a minute) would have answered it.
