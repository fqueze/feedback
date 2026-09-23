## Question: the per-test profiles of this test's failures, across jobs

- Command: `fx-tests test browser/components/urlbar/tests/unit/test_remote_tabs.js --profiles`
- Expected: for each failing job, the per-test profile of this test when one was uploaded (`profile_test_remote_tabs.js.json`, `-2` for a failed retry).
- Got: only resource-usage profiles, pointing to `fx-tests intermittent --test <path> --profiles`, which only covers the 2 sheriff-annotated jobs (and found one per-test profile). `fx-tests task DaX4ba-cRBSfLyU1RkEaDg --profiles` (tsan) said "No failing test named a per-test profile in this job".
- Workaround: `curl .../task/<id>/runs/0/artifacts` for candidate tasks and grep for `remote_tabs`, or `fx-tests task <id> --profiles` one job at a time; FG0O1-WPS0a-8yxCbpKbGg, FlW2kiiPRFqOU65gtCz0Hg, KdXd_bsrQeW9b9HvOxZuNA and others all had one.
- What could show it: a per-test-profile column (or `--per-test-profiles`) in `fx-tests test --profiles`, filterable by `--issue`, so the profiles of one failure mode are one command away.
