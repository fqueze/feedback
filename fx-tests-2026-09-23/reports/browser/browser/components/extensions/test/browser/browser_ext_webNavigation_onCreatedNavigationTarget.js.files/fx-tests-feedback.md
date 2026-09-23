
## Question: "did it fail on any build that contains the fix?" (backfills on old revisions look like a recurrence)
- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webNavigation_onCreatedNavigationTarget.js --history`
- Expected: a way to see that the 3 failures dated 2026-09-14 ran on old pushes.
- Got: counts per run date only. The 09-14 jobs (IT0S_eCQRJ-SkMnxZ3h6jA, eAt0VYKDQD2LSDmDJowgKg, VYlSTop-RXKVXW_jxzuPVw) ran on autoland pushes from 2026-08-15 (backfills), 10 days before the fix landed on 09-04, so the history reads as "came back on 09-14" when it did not.
- Workaround: Taskcluster task `created` + Treeherder `api/project/autoland/push/?revision=` for each revision's push_timestamp.
- Could have shown: the push date next to each task ID in `--task-ids` (or a flag to bucket `--history` by push date instead of run date).

## Question: "which failing test in this job ran first?" (cascade detection)
- Command: `fx-tests task QpSrFmkpR1Gt5mBA-g7xLw --profiles --messages --limit 0`
- Expected: failing tests in run order, or a note that 44 tests in one manifest failed with the same message (a cascade from one leaker).
- Got: the list is alphabetical by path, so the first failure in run order is not visible; I had to read the `test` markers in the profile.
- Workaround: `profiler-cli thread markers --category Test --search name:test --list` on the per-test profile.
