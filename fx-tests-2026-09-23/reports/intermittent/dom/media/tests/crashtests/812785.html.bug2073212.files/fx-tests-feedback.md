# fx-tests feedback (812785.html.bug2073212)

## Crashtests are not covered by `fx-tests test`
- Command: `fx-tests test dom/media/tests/crashtests/812785.html`
- Expected: rates per config, task ids.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: `fx-tests intermittent --bug 2073212`, then the Treeherder jobs API and Taskcluster task definitions, via my own scripts.

## Question: "which manifests ran in this job before my test?" (an order dependency across manifest sets)
- Commands: `fx-tests intermittent --bug 2073212 --limit 0`, `fx-tests task <taskId>`.
- The key fact was each task's `MOZHARNESS_TEST_PATHS`. On autoland, tasks with the same label (`test-macosx1015-64-qr/opt-crashtest`) run different manifest sets. Backfills and retriggers copy the failing task's set, so a harmless order dependency looked like a perma-regression on every push (216/216 fails with one set, 0/36 with others).
- What the output could have shown: the task's test-paths set in `fx-tests task`, and in `intermittent --bug` occurrences grouped by that set (and by whether the row is a backfill). Getting it took one Taskcluster fetch per task (scripts `combo_scan.py`, `paths2.py`).
