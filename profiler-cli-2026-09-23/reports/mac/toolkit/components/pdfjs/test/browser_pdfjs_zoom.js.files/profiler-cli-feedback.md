## Question: the order, to the millisecond, of the test's own log markers

- Command: `profiler-cli thread markers --thread t-0 --category Test --search browser_pdfjs_zoom --list --limit 0`
- Expected: a start time precise enough to order the test's log against content-process markers (ms).
- Got: `t=4m17s` for ten consecutive markers spanning 430 ms; the click-to-failure interval is invisible.
- Workaround: `--json` and read `flatMarkers[].start`.
- What the output could have shown: `t=4m17.028s`, or ms precision whenever the profile is longer than a minute.

