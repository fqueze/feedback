## `fx-tests test` misses failing jobs on the regressing push

- Command: `fx-tests test browser/components/enterprisepolicies/tests/xpcshell/test_sitepolicies.js --task-ids --limit 0`
- Expected: every job where the test failed, including all jobs on autoland a42a5b9dfd98 and df2509621181.
- Got: 4 jobs (win11-64 debug, win11-32 debug, win10-64 opt, macOS 15 aarch64 opt). Treeherder shows more xpcshell jobs failing on those pushes. `fx-tests task XzlgU7jJRT69GuSDfpT_2g` (linux2404-64/debug, a42a5b9dfd98) shows this test failing 2 of 2 executions, but it is not in `fx-tests test`'s count or config table.
- Workaround: listed the push's jobs through the Treeherder API and ran `fx-tests task` on the Linux one.

## The verdict says "intermittent" for a perma-fail that got backed out

- Command: `fx-tests test <path>`
- Got: "Verdict: intermittent. Fails on 5 configurations", with a 0.8% worst rate.
- What it could have shown: all 8 FAILs fall on consecutive autoland pushes on a single day, and they fail in every execution of every job (including the harness rerun). That is a regression that was backed out, not an intermittent. Grouping failures by revision or push range, with a "every execution in the job failed" flag, would have given the answer at once. It took `--history` plus `fx-tests task` on each job, and the hg push log.
- The 5th "failing configuration" (win11-64 opt-xpcshell-msix, 2026-09-21) is a single TIMEOUT in a job where 274 of 330 tests timed out. Marking job-wide collapse as such would keep it out of the per-test rate.
