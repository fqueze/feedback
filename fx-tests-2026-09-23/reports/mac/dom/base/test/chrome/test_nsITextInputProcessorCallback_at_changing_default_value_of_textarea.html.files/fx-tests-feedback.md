## `fx-tests task --profiles` misses one of the six per-test profiles

- Command: `fx-tests task YNESqZYhS-6LOzWreknitA --profiles`
- Expected: the six per-test profiles of the six failing executions: `profile_test_…textarea.html.json` and `-2` to `-6`.
- Got: five, without `profile_test_nsITextInputProcessorCallback_at_changing_default_value_of_textarea-3.html.json`. That one exists (HTTP 200, 2.8 MB), and the job's resource-usage profile has its `profile uploaded in …-3.html.json` FAIL marker at t=10m34s, logged after `this test already called finish!` and `called finish() multiple times`, which may be why it was not matched.
- Workaround: listed the `artifact` markers of the resource-usage profile.

## "The failing job this confirm-failure job confirms" has no answer

- Question: `fx-tests test <path>` shows 6 failures, all in one `-cf` job. Which regular job did it confirm, and how did the test fail there?
- Command: `fx-tests test <path> --task-ids`, then `fx-tests try 8557871b2920 --project autoland`.
- Got: the regular job (M84Z3WoXQiqaB5w4ULDPEQ) was killed at max run time, so it is only a warning line ("1 of 8 jobs were killed … not in this report"), without its task ID. `fx-tests test` counts none of its failures, so this test reads as never failing before 2026-09-20 and passing 376 of 376 on `-vms/opt-mochitest-chrome-1proc`.
- What would have answered: the killed job's task ID in the warning, and for a `-cf` job, the task it confirms.
- Workaround: the regular job's log, already downloaded by the agent diagnosing `test_nsITextInputProcessor.xhtml`.
