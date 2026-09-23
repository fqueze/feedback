## Question: "how many jobs ran this marionette test this week, and how many failed it, per config?" (same as the sibling reports)

- Command: `fx-tests test browser/components/backup/tests/marionette/test_backup_legacy_to_selectable.py`
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: reused a sibling's Treeherder job list, fetched `marionette_errorsummary.log` for each of its 143 `testfailed` jobs, and grepped them. That found 1 failure nobody annotated (a crash, SDxLNHMRTR). The list lacked one job type (`test-linux2404-64-shippable/opt-marionette-integration` on central), which I had to fetch separately from `/api/project/mozilla-central/jobs/?job_type_name=...`.
- What would have answered it: the per-config runs/failures table `fx-tests test` gives for mochitest, built from marionette errorsummaries, with the failure messages under Issues.

## Question: "every annotated failure of this bug since it was filed, on any tree"

- Command: `fx-tests intermittent --bug 2070305`
- Got: 7 annotations, trunk only, last 7 days. Treeherder's `/api/failuresbybug/?startday=2026-09-08&endday=2026-09-22&tree=all&bug=2070305` had 26. Of those, 4 were on mozilla-beta, 2 on try and 1 on debug, and 1 had a different failure mode (a crash at line 42). 2 were misannotated macOS perftest jobs.
- What would have helped: a `--since` / `--tree all` option, or a line saying how many annotations the window and tree filter left out.
