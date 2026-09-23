## Which subtest timed out, per failure

Question: "are all 76 timeouts in test_fullpageScreenshot, or also in the RTL/Scrolled subtests?"

Command: `fx-tests test browser/components/screenshots/tests/browser/browser_screenshots_test_full_page.js`
Expected: the Issues block to split `TIMEOUT` by subtest (the log line is `test_fullpageScreenshot - Test timed out`, and `fx-tests intermittent --bug 1786037` does show that text for the 2 annotated jobs).
Got: one row, `76x TIMEOUT Test exceeded time limit`, no subtest; `--task-ids` has no per-task subtest either.
Workaround: loaded profiles one by one (4 of 76) and read the TEST-UNEXPECTED-FAIL marker's Subtest field.
