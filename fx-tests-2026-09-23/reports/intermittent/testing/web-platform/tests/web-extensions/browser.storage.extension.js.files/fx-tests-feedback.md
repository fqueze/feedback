## wpt tests are not covered at all (browser.storage.extension.js, 2026-09-22)

- Command: `fx-tests test testing/web-platform/tests/web-extensions/browser.storage.extension.js` (also tried implicitly via `--harness` inference)
- Expected: rates per config and the failure modes for a wpt test, or at least a pointer to where wpt data lives.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` — nothing, and no hint that wpt is out of scope.
- Workaround: `fx-tests intermittent --bug 2055714` (only 7 days, trunk only, and "Tests named: none" because the TIMEOUT/NOTRUN lines are not TEST-UNEXPECTED-FAIL), then the Treeherder API `api/failuresbybug/?startday=...&endday=...&tree=all&bug=2055714` scripted to group by platform/build type/week and failure line. No run counts exist anywhere, so no rate.
- Question the output could have answered: "how often does this wpt test time out per config, and since when" — and, for `intermittent`, "which failure modes (by first log line) do the annotated jobs have", which needs the TEST-UNEXPECTED-TIMEOUT/NOTRUN lines, not only TEST-UNEXPECTED-FAIL.
