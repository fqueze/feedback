## Question: how often does a web-platform test fail, and how long does it take when it passes?

- Command: `fx-tests test testing/web-platform/mozilla/tests/css/css-viewport/zoom/zoom-iframe-os-zoom.html` (also tried with `--history`)
- Expected: pass/fail counts per config and a pass duration, like for mochitest/xpcshell.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." (exit 2). WPT is not covered, so there is no run count, no history and no pass duration.
- Workaround: `fx-tests intermittent --bug 1956008 --since 30` for annotation counts; `treeherder-cli --similar-history <job id>` to find a green job of the same type, then that job's `public/test_info/wptreport.json` for the passing duration (3.2 s vs the 32 s timeout).

## `fx-tests intermittent --bug` cuts the platform and job-name columns

- Command: `fx-tests intermittent --bug 1956008`
- Expected: full platform / job names in the Occurrences table (they are what tells tsan from ccov from android).
- Got: `linux2404-6…`, `web-platfor…` at the default width; `--full-messages` did not widen them.
- Workaround: `COLUMNS=250`.
