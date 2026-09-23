## Question: "does this test fail on the macOS VM configs?" — `test` says ok, while 26 jobs there failed on it

- Command: `fx-tests test browser/extensions/formautofill/test/mochitest/test_autofill_and_ordinal_forms.html --coverage --limit 0` (and `--config aarch64-vms --history`).
- Expected: the 26 `macosx1500-aarch64-vms(-shippable)` jobs that `fx-tests intermittent --bug 1803644` lists for 2026-09-17…21 to show as failures, or at least to be flagged as missing.
- Got: `test-macosx1500-aarch64-vms/opt-mochitest-plain 205 205 0 0 ok`, and a history with no failures at all. The Verdict names only the Linux Wayland configs. Every one of those jobs hit the max run time. `fx-tests task <id>` then says the profile is "a partial stream" and it cannot read it. `test` drops the jobs without saying so. Yet the per-test profiles (`profile_test_*.html.json`) and `mochitest-plain_errorsummary.log` were uploaded, and both are readable.
- What would have answered it: a line in `test` such as "N jobs on config X hit the max run time and could not be read; their errorsummary names this test". Or read the errorsummary for those jobs. And have `task --profiles` still list the per-test profile URLs of a killed job.
- Workaround: `curl` the Taskcluster artifact list, the errorsummary and the log by hand. Use Treeherder `failuresbybug` to count across trees.

## Question: "which jobs, on which workers, are behind these annotations?" — the Occurrences table cuts the columns that answer it

- Command: `fx-tests intermittent --bug 1803644 --limit 0`
- Expected: the full job name (`test-macosx1500-aarch64-vms/opt-mochitest-plain-3`) and ideally the worker, for each occurrence.
- Got: `macosx1500-…` and `mochitest-p…` in every row, even with plenty of terminal width left. So VM and non-VM jobs, and chunks, cannot be told apart per row. The worker is not shown at all, though Treeherder's failuresbybug has `machine_name`.
- Workaround: one Taskcluster `task/<id>` and `task/<id>/status` call per task ID, and Treeherder `failuresbybug` for the machine names. It would also help to have a way to widen the table past the cut, as `--full-messages` does for messages.
