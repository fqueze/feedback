## Question: which failure-line variant and revision each annotated occurrence of a bug had

- Command: `fx-tests intermittent --bug 2033213 --tree all --since 21 --limit 0`
- Expected: the Occurrences table showing each job's revision and failure-line hash. The bug had two variants, `0e9566a1...` (16 jobs) and `5a889ab4...` (9 jobs), and I needed to know which jobs had which, and whether the last failure predated a fix's landing.
- Got: push time and task id only. The variants are aggregated under "Failure messages", and there is no revision column.
- Workaround: `--json`, then a script over `occurrenceRows[].revision` and `.lines`.

## Question: did a WPT test pass on its config after a fix landed

- Command: `fx-tests test testing/web-platform/tests/fullscreen/rendering/exit-fullscreen-scroll-to-unscrollable-area-overflow-x-hidden.html`
- Expected: per-config runs and passes, or a pointer to where to get them.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data". WPT is not covered. With only sheriff annotations, "no annotation since date X" cannot be told apart from "not run since X".
- Workaround: listed central pushes with the Treeherder API, filtered the jobs to windows ccov wpt-reftest, and grepped each job's live_backing.log for the test's TEST-PASS line.
