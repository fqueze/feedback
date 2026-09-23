## A TEST-KNOWN-FAIL (todo) message is used as the failure label

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadCore.js` (Issues block), and `fx-tests task <id>` (first message per failing test).
- Expected: the message that made the run fail — e.g. `Test timed out`, `File does not exist: C:\...\xpcshell-download-test.txt`, `Unexpected exception NotFoundError: Could not get extended attribute com.apple.metadata:kMDItemWhereFroms ...`.
- Got: `FAIL 31 == true` (191 runs) and, in `fx-tests task`, `TIMEOUT — 31 == true` as the first message. `31 == true` is an expected failure: the per-test profile records it as `TEST-KNOWN-FAIL`, and the job log prints it as `TEST-FAIL | ... | test_basic - 31 == true` (no `UNEXPECTED`). The whole test emits it ~25 times per run, passing or not.
- Effect: three distinct failure modes (a Windows file-not-found, a macOS xattr exception that also fails the retry, and some timeouts) are merged under one misleading label; I had to open individual tasks with `--messages` to separate them.
- Workaround: `fx-tests task <id> --messages --full-messages` per task.

## `--task-ids --issue 4` repeats a block of dates after the list

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadCore.js --task-ids --limit 0 --issue 4`
- Expected: one chronological list of the 177 jobs.
- Got: the list runs 2026-08-31 … 2026-09-18, then an unlabelled second block starting again at 2026-09-01 with some of the same task IDs (e.g. `JsB7vCAKRQ21Cdw3uz9tEw.0`, `Qhbs_hacR7e7Y8VbpTRlZA.0` listed twice, the latter under 2026-09-08 the second time).
- Workaround: none needed, but counts derived from the list are inflated.
