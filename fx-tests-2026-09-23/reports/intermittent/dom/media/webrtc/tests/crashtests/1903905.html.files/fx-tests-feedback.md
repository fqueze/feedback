## Crashtests are not covered by `fx-tests test`

- Command: `fx-tests test dom/media/webrtc/tests/crashtests/1903905.html`
- Expected: rates per config, history, failing task ids for a crashtest (reftest harness).
- Got: `No test path in the xpcshell and mochitest 21-day data contains "dom/media/webrtc/tests/crashtests/1903905.html"`.
- Workaround: `fx-tests intermittent --bug 2046290` for the last 7 days, then Treeherder's
  `/api/failuresbybug/?bug=2046290&startday=...` for the history since the bug was filed, and
  `/api/project/autoland/jobs/<job_id>/similar_jobs/` for the pass/fail denominator per config.
  `fx-tests task <taskId>` did work for crashtest jobs.

## Question: "on which workers did the annotated failures run?"

- Command: `fx-tests intermittent --bug 2046290 --limit 0`
- The occurrence table has task ids but no worker/machine name, so answering "is this tied to
  some machines?" needed a script over Treeherder's `failuresbybug` (which has `machine_name`) and
  over `similar_jobs` for the passing denominator per machine. Here the answer was the whole
  diagnosis: all recent failures ran on macmini-m4-153/174-183/197-215, none on ~480 jobs
  elsewhere. A `--by-worker` breakdown (failures vs runs per worker) would have shown it directly.
- Also: `intermittent` only covers 7 days; the bug's history since 2026-06-10 had to come from
  Treeherder.
