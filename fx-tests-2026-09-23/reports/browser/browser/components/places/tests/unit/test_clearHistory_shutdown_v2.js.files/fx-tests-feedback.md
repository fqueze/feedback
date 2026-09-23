# fx-tests feedback (test_clearHistory_shutdown_v2.js diagnosis)

## `fx-tests crash` does not find a minidump that is in the task's artifacts

- Command: `fx-tests crash TlCpCRaLSIu301MhTfCJgQ 4bafc9aa-e524-495a-a5ef-2d03cf4ad515 --all-threads`
- Expected: the dump read (or at least "the dump is empty" — the log says `Missing minidump header (empty minidump?)`).
- Got: `no minidump 4bafc9aa-... on task TlCpCRaLSIu301MhTfCJgQ.0: the artifact is not there.` and a hint that Taskcluster expired it.
- Reality: the artifact exists, unexpired (expires 2027-09-16), at `public/test_info/4bafc9aa-e524-495a-a5ef-2d03cf4ad515.dmp`. The id came from the resource-usage profile's CRASH marker.
- Workaround: listed the artifacts with `curl .../runs/0/artifacts`, and read the live log to learn the dump was empty.

## Question: "which of this test's failures are job-wide, where the test never ran?"

- Command: `fx-tests test browser/components/places/tests/unit/test_clearHistory_shutdown_v2.js` (and `--task-ids`)
- Expected: some sign that 8 of the 12 TIMEOUTs are jobs where hundreds of tests "timed out" together (msix jobs where `CreateProcess` failed with `[WinError 5] Access is denied`, so the test never started), and 1 of the 2 FAILs is a job where 55 tests failed with the same NS_ERROR_FILE_CORRUPTED.
- Got: all 14 counted as this test's failures, 2.5% on msix presented as the worst config.
- Workaround: ran `fx-tests task <id> --profiles` on all 13 tasks and read the "N tests, M failing" header of each. The per-task failing count, next to each task id in `--task-ids`, would have answered it.

## `--bugs` shows nothing although a bug names the test

- Command: `fx-tests test browser/components/places/tests/unit/test_clearHistory_shutdown_v2.js --bugs`
- Expected: bug 2055240 ("Perma [tier2] browser/components/places/tests/unit/test_clearHistory_shutdown_v2.js | single tracking bug", RESOLVED FIXED 2026-07-15). It turned out to be the key clue.
- Got: no bugs section at all, not even "none found". `annotatedBugs` in `--json` is there but `bugs` is null.
- Workaround: Bugzilla REST `bug?summary=clearHistory_shutdown_v2`.
