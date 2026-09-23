## The failure message shown is a TODO pass, not the failure

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadLegacy.js` (Issues block), `fx-tests task FJATl84VR8iclfuypyQVbA --profiles`.
- Expected: `TIMEOUT Test timed out` for the timeouts; for the FAIL rows the first unexpected failure, e.g. `[test_blocked_parental_controls : 2081] false == true` (Windows) or `Unexpected exception NotFoundError: Could not get extended attribute com.apple.metadata:kMDItemWhereFroms ...` (macOS).
- Got: Issues row 4 `182x FAIL 31 == true`, and `31 == true` as the message of TIMEOUT runs in `fx-tests task`. `31 == true` is `todo_check_true(downloadTarget.size)` in `expectNonZeroDownloadTargetSize`, a known failure that the replayed log prints as `TEST-FAIL` (the profile has it as `TEST-KNOWN-FAIL`/`TODO`) and that every run of this test emits, passing or failing. The Issues block therefore lumps three different failure modes (macOS xattr missing, several Windows ones) under one message that is none of them.
- Workaround: `fx-tests task <id> --messages` per task and reading past the `31 == true` / `49 == true` / `62 == true` lines, or reading the job log. There is no way to get the real per-mode counts for the 182 FAIL runs short of iterating over every task.

## Question: how long does the test take when it passes in the parallel phase

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadLegacy.js --durations --limit 0`.
- Expected: the duration distribution of first attempts, or per execution mode, to see how close the passing parallel runs come to the 30 s timeout.
- Got: one distribution per config, `test-macosx1500-aarch64/opt-xpcshell 240 runs, median 6160 ms, p95 26957 ms`. On that config 159 of 413 runs timed out, and each of those jobs retried the test sequentially, where it passes in ~6 s; those retries are in the 240 passes, so the median mostly measures retries, not the parallel phase where the test fails.
- Workaround: none from fx-tests; read the retry durations from the resource-usage profiles of individual jobs. `--durations` could split by execution mode, as `--executions` already does for failures.
