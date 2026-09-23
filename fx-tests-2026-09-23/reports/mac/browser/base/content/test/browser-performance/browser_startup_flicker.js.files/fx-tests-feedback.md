## `todo()` messages (TEST-KNOWN-FAIL) reported as the failure

- Command: `fx-tests test browser/base/content/test/browser-performance/browser_startup_flicker.js` (also the old path `.../test/performance/...`), and `fx-tests task asLJFw5XRxiUMMn8hUqllg --messages`.
- Expected: the Issues block ("first failure per run") and the task's message list to show the TEST-UNEXPECTED-FAIL messages, e.g. `unexpected changed rect: ({x1:980, x2:1279, y1:84, y2:948, w:300, h:865})`.
- Got: Issues rows 2-4 are `bug 1445161 - the window should be focused at first paint, []` and `urlbar focus ring settles during startup idle tasks (bug 2066735)`, which are `todo(false, ...)` calls in the test: the profile shows them as TEST-KNOWN-FAIL markers (m-12..m-41 in that task's per-test profile). `--messages` mixes them with the real TEST-UNEXPECTED-FAIL ones with nothing telling them apart. The whole Issues block, and `--issue <n>` task grouping, is therefore keyed on a message that is not a failure, so every failure mode of this test collapses into the same row.
- Workaround: read the test's `--category Test` markers in each profile to find the TEST-UNEXPECTED-FAIL messages.

## `test --bugs` prints no bugs section at all

- Command: `fx-tests test browser/base/content/test/performance/browser_startup_flicker.js --bugs` (same for the `browser-performance/` path).
- Expected: a section listing the sheriff-annotated bugs naming the test, or an explicit "none found". `fx-tests intermittent --bug 2063231 --since 30 --tree all` shows 3 annotations on this test (W-nISF_4R1KC_jA_6R5CWg, JAMDfaEGTpatd9h00JBD8w, dMXVuG2rRSCO1u20mGsufw), and the bug's summary names the test.
- Got: the ordinary `test` output, ending with the Issues block, exit 0; nothing about bugs, so "no bugs" and "the lookup did not run" look the same.
- Workaround: `fx-tests intermittent --bug <N> --since 30 --tree all` for each bug I already knew about.

## Test moved directory mid-window: its history is split across two paths

- Command: `fx-tests test browser/base/content/test/browser-performance/browser_startup_flicker.js --history`.
- Expected: some hint that the test existed under another path (bug 2069131 renamed `test/performance/` to `test/browser-performance/` on 2026-09-16/18).
- Got: a history starting on 2026-09-16 with 12 failures, which reads as "new test"; the 82 earlier failures, and the 2026-09-08 step change, are only under the old path.
- Workaround: guessed the old path from the bug summaries and ran every command twice.

