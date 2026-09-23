# fx-tests feedback (test_browserGlue_corrupt.js)

## Question: "which bug were this test's failing jobs starred with?"
- Command: `fx-tests test browser/components/places/tests/unit/test_browserGlue_corrupt.js --bugs`
- Expected: the bug sheriffs annotated this test's failing jobs with (here bug 1991833, "High frequency win-msix PermissionError: [WinError 5] Access is denied"; 10 annotations in the last 7 days).
- Got: nothing, because no bug summary names the test. The failing jobs' own annotations were not consulted.
- Workaround: Bugzilla quicksearch on "msix WinError 5", then `fx-tests intermittent --bug 1991833`.

## Question: "were this test's failures part of a job-wide breakdown?"
- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: next to each task ID, how many other tests failed in that job (here 274 to 1,563 TIMEOUTs per job), so a bystander in a broken job is visible without opening each job.
- Got: task IDs only; had to run `fx-tests task <id>` 9 times to see the "N failing" header.

## Question: "is there a per-test profile for my test in this job?"
- Command: `fx-tests task ZgUlY0ZlRvmHZNTOfia3AA --profiles`
- Expected: a way to restrict the listing to one test (e.g. `--test <path>`).
- Got: first ~20 failing tests then "… 255 more"; needed `--limit 0` and grep through 275 entries to learn my test had none.
