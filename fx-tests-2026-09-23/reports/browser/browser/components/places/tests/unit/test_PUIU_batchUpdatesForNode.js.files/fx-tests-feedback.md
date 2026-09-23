## `fx-tests test` does not flag failures that come from whole-job breakage

- Command: `fx-tests test browser/components/places/tests/unit/test_PUIU_batchUpdatesForNode.js` (and `--task-ids --limit 0`)
- Expected: some sign that all 10 failures come from broken jobs. In the 8 msix TIMEOUT jobs, 274–1,563 tests failed per job; the 1 win32 FAIL job had 55 tests failing with the same NS_ERROR_FILE_CORRUPTED. For example, a per-job "N other tests failed in this job" column in `--task-ids`, or a verdict line such as "all failures are in jobs where >100 tests failed".
- Got: the verdict "intermittent. worst is ...msix at 2.5%" and the two Issues listed as if test-specific. Only running `fx-tests task` on each of the 9 task IDs revealed it.
- Workaround: loop `fx-tests task <id> | head -4` over the task IDs.

## `fx-tests test --bugs` misses the bugs these failures are starred with

- Command: `fx-tests test <path> --bugs`
- Expected: bug 1991833 (msix WinError 5), on which sheriffs annotated ZgUl and OgH9, two of this test's failing jobs. Bug 2050422 would be good too (the harness bug behind the readUserPrefsFromFile FAIL).
- Got: nothing, since neither summary names the test.
- Workaround: Bugzilla quicksearch on the job's harness error, then `fx-tests intermittent --bug 1991833`, whose occurrence list contains ZgUl/OgH9. The question was "which bug are this test's failing jobs starred with", and the job → annotation mapping is what could answer it.
