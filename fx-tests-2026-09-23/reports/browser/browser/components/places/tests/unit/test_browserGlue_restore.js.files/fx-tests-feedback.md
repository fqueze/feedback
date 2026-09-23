## `fx-tests test --bugs` prints nothing when no bug names the test
- Command: `fx-tests test browser/components/places/tests/unit/test_browserGlue_restore.js --bugs`
- Expected: a "Bugs" section, even if it says "none", ideally also listing the bugs the failing jobs were starred with.
- Got: output identical to the run without `--bugs`; JSON `annotatedBugs: []`. Two of the failing jobs (ZgUlY0ZlRvmHZNTOfia3AA, OgH9QhqjS4a-6VFFNGQrEg) are starred on bug 1991833, which doesn't name the test, so it never showed up.
- Workaround: Bugzilla quicksearch on the error text, then `fx-tests intermittent --bug 1991833` to confirm the task IDs.
- Question it could answer: "which bug were this test's failing jobs starred with?"

## Question: "is this failure specific to the test, or did the whole job break?"
- Commands: `fx-tests test <path> --task-ids`, then `fx-tests task <id>` on each of the 9 jobs, one by one.
- All 8 TIMEOUT jobs had 274 to 1563 other TIMEOUTs; the FAIL job had 55 tests failing the same way. `fx-tests test` still says "Verdict: intermittent" with no hint.
- Could have shown: next to each task ID (or in the verdict), how many other tests failed in that job, and a flag like "every failing job was a job-wide meltdown".

## Question: "a passing job of the same config, to compare against"
- No command lists passing task IDs for a test/config (`--task-ids` lists failures only). Used the Treeherder API (`/api/project/autoland/jobs/?job_type_name=...&result=success`) instead.
