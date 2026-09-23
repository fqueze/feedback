## Question: which jobs failed on this test on a config whose failing tasks were killed at max run time?

- Command: `fx-tests test browser/extensions/formautofill/test/mochitest/creditCard/test_basic_creditcard_autocomplete_form.html --coverage --limit 0`
- Expected: macosx1500-aarch64-vms/opt shown as failing. `fx-tests intermittent --bug 1777066` lists 30 sheriff-starred jobs on that config in 7 days.
- Got: `test-macosx1500-aarch64-vms/opt-mochitest-plain  182 182 0 0 ok`. The failing jobs are absent because they were aborted at maxRunTime and wrote only a partial resource profile. `--history --config macosx1500-aarch64-vms` just shows the pass count halving.
- Workaround: pulled the job list from the Treeherder jobs API, read `MOZHARNESS_TEST_PATHS` from each task definition, and checked each task's artifact list for per-test profiles. That took about 200 requests in a script.
- What would have answered it: count killed or unreadable jobs whose test paths include the test's manifest, per config, as "unknown/aborted" rather than dropping them. At least warn when `intermittent --bug` finds starred jobs that `test` cannot see.

## Question: the per-test profiles of a task killed at max run time

- Command: `fx-tests task A0wbwr01SLWU1ug1n2B6lw.0 --profiles`
- Expected: the per-test profile URLs. The task has `profile_test_basic_creditcard_autocomplete_form.html.json`, its `-2`, and 8 others as artifacts.
- Got: "was killed for exceeding its maximum duration ... this tool does not read that format", and nothing else.
- Workaround: `curl .../runs/0/artifacts` and filtered on `profile_`.
- What would have answered it: list the `profile_test_*` artifacts even when the resource profile is unreadable.

## Question: what distinguishes the 19 timeouts of this test?

- Command: `fx-tests test <path> --task-ids --limit 0`
- Got: a single issue, `TIMEOUT Test exceeded time limit`, for all 19.
- The discriminator was in the log lines just before the timeout: `savedFieldNames is null` (13, all linux2404-64-artifact), `Services.ww.activeWindow is null` / `Unable to restore focus` (5, Wayland) and neither (1, Windows).
- Workaround: fetched each task's live_backing.log and grepped between this test's TEST-START and TEST-TIMEOUT.
- What would have answered it: the JS errors, or the first ERROR/JavaScript error lines, logged during the failing test, grouped per task.
