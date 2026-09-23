# fx-tests feedback (browser_storage_cookies_delete_all.js)

## `task` and `try` headline a TEST-KNOWN-FAIL instead of the failure

- Command: `fx-tests task UJYKq40TR8-9bVYal61Yig --profiles` (also `fx-tests try 9db28f6db8bf... --all-jobs --json`, `messages[0]`)
- Expected: the first message is the TEST-UNEXPECTED-FAIL, `There is correct number of rows in cookies > https://sectest1.example.org - Got +0, expected 6`.
- Got: `handleEvent() was unable to perform a11y checks on hidden node: id: storage-table-popup-delete-all-from, ...`. The profile marks that one TEST-KNOWN-FAIL. `--messages` mixes the three known-fails in with the seven real failures, and does not say which is which.
- Workaround: read the test's Test-category markers in the per-test profile to find which message was the unexpected one.

## Question: on which jobs of this try push did the test run, and did it pass there?

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --all-jobs --limit 0` (and `--json`: `totalRuns: 3, totalJobs: 2`)
- Expected: the jobs that ran the test, and each one's outcome. It ran in 5 runs across 4 jobs: opt devtools-chrome-1 `UJYKq40TR8-9bVYal61Yig` (failed, then passed on retry), opt devtools-chrome-1 `LIciRu8KRdqikyi_HKwwEw` (pass), and debug devtools-chrome-1 `LHYMBSUkQna5sRfy55AVNg` and `S1NkKWBhQIKZSHskhIK3iw` (pass each).
- Got: "1/3" runs, over 2 jobs, and only the failing job named. The counts are lower than the real ones, and the jobs where it passed are not listed.
- Workaround: list the push's devtools-chrome-1 jobs from the Treeherder API, then run `fx-tests task <id> --passed --limit 0` on each and grep for the test.
- Without `--all-jobs`, the test is absent from `fx-tests try` altogether, because its job went green on retry. That is documented, but the brief's step ("If it did not fail there, check that it ran") steers you to read "absent" as "passed".
