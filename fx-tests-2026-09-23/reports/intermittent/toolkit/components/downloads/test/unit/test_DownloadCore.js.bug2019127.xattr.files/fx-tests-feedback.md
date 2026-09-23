## Question: which workers did these failures run on, and did the test ever pass on those workers?

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadCore.js --task-ids --issue 4`, then `fx-tests task <id>` for each.
- Expected: a machine/worker column on each task ID, and a way to list a test's runs (passing ones too) on one worker. This failure mode happens only on 5 of 173 macOS M4 workers, every time the test runs there. Nothing in fx-tests shows it.
- Got: task IDs and job names only.
- Workaround: the Treeherder jobs API (`/api/project/<repo>/jobs/?job_type_name=...`, which has `machine_name`), then `fx-tests task <id> --json` on each of the workers' jobs to see whether the test ran. About 80 requests.

## A failing job is missing from `fx-tests test --task-ids`

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadCore.js --task-ids --limit 0 --issue 3` and `--issue 4`.
- Expected: NMtwa2q8QyiiPsH1x038lg (autoland, test-macosx1500-aarch64/debug-xpcshell, 2026-09-11) to be listed. `fx-tests task NMtwa2q8QyiiPsH1x038lg` shows test_DownloadCore.js failing 2 of 2 executions.
- Got: it is in neither list.
- Workaround: Treeherder `bug_suggestions` for every failed job of the config, grepping the message.
