# fx-tests feedback (pointerevent_coordinates_when_locked.html, bug 2051017)

## `fx-tests test` has no web-platform-tests data

- Command: `fx-tests test testing/web-platform/tests/pointerevents/pointerlock/pointerevent_coordinates_when_locked.html`
- Expected: rates per config, history, failing task ids for a WPT test.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." (`--harness` only accepts xpcshell|mochitest).
- Workaround: paged the Treeherder jobs API for every wpt testharness job per platform, and read each job's `wpt_errorsummary.log` (its `test_groups` line says whether the group ran, its `test_result` lines give non-expected results) with a script. Took ~2000 artifact fetches to get a pass/fail count.

## `fx-tests task` does not list a failing subtest whose test status is OK

- Command: `fx-tests task Gb8uBNXAQACw7BNTq3QSLg --profiles`
- Expected: `/pointerevents/pointerlock/pointerevent_coordinates_when_locked.html` among the failures: its log and errorsummary have `TEST-UNEXPECTED-FAIL ... mouse Test pointerevent coordinates when pointer is locked - assert_equals: clientX expected 146 but got 640`, and the resource-usage profile has a `FAIL` marker for it.
- Got: "FAILED (8)" listing only tests whose top-level status was ERROR; this test (top-level `TEST-OK`) was counted among "749 OK".
- Workaround: read the job log / errorsummary directly.

## Question: which occurrence of a bug is which failure message

- Command: `fx-tests intermittent --bug 2051017 --limit 0`
- The text output gives message counts and an occurrence list, but not which occurrence carried which message, nor that 33 of 109 rows had no failure line at all. Needed `--json` (`occurrenceRows[].lines`) to find that the macOS pre-fix rows were mostly empty and all Linux rows were this test.
- Could have shown: a message/test column (or "no lines") in the occurrence table.
