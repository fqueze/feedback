# fx-tests feedback (test_tree_14.html report)

## Question: how often does this test fail on its regular config, counting jobs killed at max run time?

- Command: `fx-tests test devtools/client/shared/components/test/chrome/test_tree_14.html` (and `--coverage`)
- Expected: some sign that the regular `test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc` config has jobs whose results are missing, since 36 of its last 98 completed jobs were killed at the 3600s max run time (Treeherder similar-jobs history), several of them with this test failing (e.g. d6XM1k2_Sjquqb1U6OjEpQ, first run and retry).
- Got: `288 pass 0 fail` on that config and a verdict of "Fails on 1 configuration" (the `-cf` one), with no mention that killed jobs are excluded. `fx-tests task d6XM1k2_Sjquqb1U6OjEpQ` then says the job "was killed for exceeding its maximum duration" and reads nothing, although the job has a `mochitest-chrome_errorsummary.log` and per-test profiles (`profile_test_tree_14.html.json`, `-2`) that name the failure.
- Workaround: `treeherder-cli --similar-history <job id> --json` for the per-day killed count, the job's errorsummary for its failures, and `curl` of the task's artifact list for the profile URLs.
- What would have answered it: a line under the per-config table such as "N jobs of this config were killed at max run time in the window and are not counted", and `fx-tests task` falling back to the errorsummary and the `profile_*.json` artifact list for a killed job.

## Question: which job did this confirm-failure (`-cf`) job confirm?

- Command: `fx-tests task DVp16B5WSkWdLSb3Ww4A1Q --profiles`
- Expected: the original failing job, since a `-cf` job only reruns a test that failed elsewhere.
- Got: nothing about the parent; I had to read the task's `extra.parent` (the action task), then that task's `ACTION_TASK_ID` env to reach d6XM1k2_Sjquqb1U6OjEpQ.
- What would have answered it: "confirm-failure of <taskId>" in the header of `fx-tests task` for a `-cf` job.
