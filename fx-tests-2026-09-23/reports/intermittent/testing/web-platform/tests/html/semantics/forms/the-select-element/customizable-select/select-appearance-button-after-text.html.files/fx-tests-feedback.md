## `fx-tests test` has no web-platform-tests data
- Command: `fx-tests test testing/web-platform/tests/html/semantics/forms/the-select-element/customizable-select/select-appearance-button-after-text.html` (also `--history`)
- Expected: rates per config / history for a WPT reftest.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: Treeherder `api/failuresbybug/?bug=2070778`, then per-push job listings and scraping each task's `wptreport.json`. `fx-tests task <id>` did work for WPT jobs.

## Question: "what did this one test render on every config of one push?"
- Needed: status plus test/reference screenshot hashes of one WPT reftest across all reftest tasks of central push f3bc7391657f, to tell a deterministic FAIL from a bimodal one.
- Command: a script that lists the task group through the Taskcluster queue API and downloads 161 `wptreport.json` files (the Treeherder jobs API with pagination timed out after 2 min).
- What could show it: `fx-tests test <wpt path> --coverage` with the reftest hash from wptreport per config.

## `fx-tests intermittent --bug` counts only trunk
- Command: `fx-tests intermittent --bug 2070778`
- Got: "1 sheriff annotations on trunk". Treeherder has 15 in the same 7 days, 13 of them on mozilla-beta. Not wrong, but a bug whose occurrences are almost all on beta reads as nearly dead. Consider a line with the non-trunk count.
