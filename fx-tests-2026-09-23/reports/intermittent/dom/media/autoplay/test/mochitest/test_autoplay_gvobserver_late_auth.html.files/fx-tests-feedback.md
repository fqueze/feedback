## `--history` shows a step change on 2026-09-14 that is a reporting change, not a regression

- Command: `fx-tests test dom/media/autoplay/test/mochitest/test_autoplay_gvobserver_late_auth.html --history`
- Expected: failures on every weekday of the window. Treeherder's `failurecount` API for bug 1989173 shows 20-37 annotated failures per weekday from 2026-08-17 to 2026-09-13.
- Got: 0 fail / 0 timeout every day 2026-09-01 .. 2026-09-13, then 10/27/7 timeouts on 09-14/15/16. The start matches the landing of bug 2062615 ("Report timeouts and crashes against the test that failed", autoland 2026-09-14 ~09:37 UTC), which made Android "application timed out after 370 seconds with no output" aborts emit a structured test_end for the running test. Before it, these aborts were not attributed to this test in the fx-tests data.
- Workaround: cross-checked with `https://treeherder.mozilla.org/api/failurecount/?startday=...&endday=...&tree=all&bug=1989173`.
- What could have shown it: a note in `--history` (or the verdict) when a harness-attribution change falls inside the window, or a per-day count of annotated failures for the test's bugs next to fx-tests' own counts. Anyone reading `--history` alone would date this regression to 2026-09-14 and bisect the wrong range.

## The revisions behind the failing tasks, to bracket the landing that stopped it

- Command: `fx-tests test <path> --task-ids --limit 0` (and `--json`)
- Question: "which push was the last to fail, and the first after it to pass?"
- Got: task ids with day only; no revision, repo or push time. Needed one Taskcluster `queue/v1/task/<id>` request per task (44) to read `GECKO_HEAD_REV`.
- What could have shown it: repo + short revision (and push time) on each task-id row.
