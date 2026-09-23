## `intermittent --bug` occurrence table hides the discriminating config at default width

- Command: `fx-tests intermittent --bug 1921148`
- Expected: the platform and job name of each occurrence, readable (asan vs tsan, fission vs nofis).
- Got: `linux2404-6…` and `web-platfor…` in the Occurrences table, so the config could not be read.
- Workaround: `COLUMNS=250 fx-tests intermittent --bug 1921148 --full-messages`.

## Question: "how many runs of this WPT test, per config" (the denominator of a WPT intermittent)

- Command: `fx-tests test testing/web-platform/tests/html/browsers/the-window-object/window-aliases.html`
- Got: "No test path in the xpcshell and mochitest 21-day data…". This was expected, because there is no WPT data. The result: frequency could only be given as annotated failure counts (Treeherder failuresbybug), with no run counts or rates.
- Workaround: `curl https://treeherder.mozilla.org/api/failuresbybug/?startday=…&bug=N` for counts, and per-task `wptreport.json` for durations in passing jobs.
