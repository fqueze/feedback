## `fx-tests test <path> --bugs` finds no bug, but two open bugs name the test

- Command: `fx-tests test devtools/client/application/test/browser/browser_application_panel_telemetry-debug-worker.js --bugs`
- Expected: bugs naming the test, among them the open single tracking bug 2031185 ("Intermittent ...telemetry-debug-worker.js | single tracking bug", NEW, 9 sheriff annotations in the last 30 days) and bug 2034015 (NEW, the TV `source is undefined` bug).
- Got: the same output as without `--bugs`, with no bug section and no "no bugs found" line.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=<test file name>"`.
