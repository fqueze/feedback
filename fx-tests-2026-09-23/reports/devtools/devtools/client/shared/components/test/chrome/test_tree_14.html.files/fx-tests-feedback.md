# fx-tests feedback (test_tree_14.html, 2026-09-22)

## Question: how many runs of this test failed, counting jobs killed at max run time?

- Command: `fx-tests test devtools/client/shared/components/test/chrome/test_tree_14.html` (and `--coverage`)
- Expected: the failures in `test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc`, where 20 of the last 100 jobs (2026-09-17..21) failed this test on the first run and on the retry.
- Got: that config shown as `285 runs 285 pass 0 fail ... ok`; the only failures visible are the 10 from one `-cf` job. The verdict ("Fails on 1 configuration, 83.3% (10/12)") hides that the parent config's failing jobs were never read.
- Why: those jobs hit the 1 h max run time, and `fx-tests task d6XM1k2_Sjquqb1U6OjEpQ` says "killed for exceeding its maximum duration ... there are no per-test results to read". But the task *did* upload `mochitest-chrome_errorsummary.log` and the per-test `profile_*.json` files.
- What it could have shown: per config, the number of jobs killed at max run time (unread), and ideally the failures from their `errorsummary.log`. At minimum, a warning on `fx-tests test` that N jobs of a config were unreadable.
- Workaround: the Treeherder `similar_jobs` API for the job type, then one `curl` of `mochitest-chrome_errorsummary.log` per failing job (45 jobs), grepped for the test.

## Question: which per-test profiles does a job killed at max run time have?

- Command: `fx-tests task d6XM1k2_Sjquqb1U6OjEpQ --profiles`
- Expected: the list of per-test profiles, which the task uploaded (`profile_test_tree_14.html.json`, `-2`, and 20 others).
- Got: exit with "The job never got to write a profile, so there are no per-test results to read."
- Workaround: listing the task's artifacts through the Taskcluster queue API.
