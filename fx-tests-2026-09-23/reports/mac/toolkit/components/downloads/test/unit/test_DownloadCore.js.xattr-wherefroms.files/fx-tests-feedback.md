## `test --task-ids --issue` prints the macOS tasks twice

- Command: `COLUMNS=300 fx-tests test toolkit/components/downloads/test/unit/test_DownloadCore.js --task-ids --limit 0 --issue 4`
- Expected: each of the 177 jobs listed once, under its date.
- Got: after the last date (2026-09-18), the date headers restart at 2026-09-01 with no section header, and the 14 macOS jobs are listed a second time. Counting `macosx` lines gives 28 instead of 14; an earlier report on this test did exactly that ("up to 28 macosx1500 jobs").
- Workaround: `sort -u` on the task lines.

## The issue label hides the failure mode: `FAIL 31 == true` is a TEST-KNOWN-FAIL

- Question: "which jobs of this test failed with the `kMDItemWhereFroms` NotFoundError?"
- Command: `fx-tests test <path> --task-ids --issue 4`, then `fx-tests task <id> --messages --full-messages` once per job (15 calls) and grep.
- Expected: `Issues` grouping the FAILs by their first *unexpected* message, so the xattr mode (14 macOS jobs, 28 runs) and the Windows file-name race (~160 jobs) are two rows.
- Got: all 191 FAILs under `31 == true`, which the test logs as `TEST-KNOWN-FAIL` in every run, passing or not.
- Workaround: a loop over `fx-tests task --messages` per job.

## A failing job missing from `test --task-ids`

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadCore.js --task-ids --limit 0`
- Expected: every job in the window where the test failed.
- Got: `NMtwa2q8QyiiPsH1x038lg` (autoland bdafb88f764b, test-macosx1500-aarch64/debug-xpcshell, 2026-09-11) is not listed, though `fx-tests task NMtwa2q8QyiiPsH1x038lg` shows `test_DownloadCore.js` FAIL, 2 failing executions of 2, with the same `kMDItemWhereFroms` message as the 14 listed jobs. Found through Treeherder's `jobs/?machine_name=` instead.
- Workaround: none within fx-tests; the per-config counts may undercount slightly.

## Question: "on which workers does this failure happen, and does the test pass on those workers?"

- Commands: Taskcluster `task/<id>/status` per job for the `workerId`, then Treeherder `api/project/<repo>/jobs/?machine_name=<worker>` (paginated), then `fx-tests task <id> --passed --limit 0` for ~230 jobs to see whether `test_DownloadCore.js` ran and how it ended.
- What would have answered it: a worker column in `test --task-ids`, and a `--worker` breakdown in `test` (runs, fails, per worker) — here the failure mode was 100% on 5 of 172 workers and 0% on the rest, which a per-worker table shows at a glance.
