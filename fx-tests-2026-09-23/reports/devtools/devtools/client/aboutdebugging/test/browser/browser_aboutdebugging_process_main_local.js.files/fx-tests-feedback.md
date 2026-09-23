## Question: which pushes ran this test on one config, and did each run pass? (locating the landing that stopped a failure)

- Command: `fx-tests test <path> --task-ids --limit 0` and `fx-tests test <path> --day <d> --coverage --config standalone`
- Expected: the task IDs (and revisions / push times) of the *passing* runs too, or a per-push pass/fail list for one config, so the last failing and first passing push can be read off directly.
- Got: task IDs only for failures; passes are per-day counts. `--json` has no passing task IDs either.
- Workaround: Treeherder `/api/jobs/?push_id=..&job_group_symbol=M-sa&platform=windows11-64-25h2` for every push in the range, `manifests.list` per chunk to find the one holding the test, then `fx-tests task <id>` on each (job result alone does not show a failure that passed on the harness rerun, and `errorsummary.log` omits those too). About 70 tasks, several minutes.
- What could have shown it: `fx-tests test <path> --config <c> --runs` listing every run (pass or fail) with revision, push time and task ID.
