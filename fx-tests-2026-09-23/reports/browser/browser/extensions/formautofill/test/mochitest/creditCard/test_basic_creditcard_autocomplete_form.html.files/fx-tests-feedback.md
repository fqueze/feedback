## Jobs killed for max-run-time are invisible to `fx-tests test`, hiding a test's most frequent failure

- Command: `fx-tests test browser/extensions/formautofill/test/mochitest/creditCard/test_basic_creditcard_autocomplete_form.html` (and `--coverage`, `--config macosx1500-aarch64-vms --history`)
- Expected: the macosx1500-aarch64-vms configs to show failures. Bug 1777066 has 30 sheriff annotations in 6 days, all on those configs (`fx-tests intermittent --bug 1777066`), and Treeherder shows ~140 mochitest-plain jobs on that pool killed at max-run-time 2026-09-16..21.
- Got: `test-macosx1500-aarch64-vms/opt-mochitest-plain 182 runs 182 pass 0 fail`, verdict "worst is linux2404-64-artifact/debug ... 9.3%". The killed jobs are simply absent (their profile is a partial stream), so the headline rate and worst config describe the minor failure mode. Only the daily pass count dropping (~15/day to ~8/day) hints at it.
- Workaround: `fx-tests intermittent --bug`, then Treeherder's jobs API per job_type_name, filtering on duration > 3500 s.
- Could show: "N jobs of this config were killed / have no readable profile in the window" per config, and in `--history`.

## `fx-tests task` refuses killed tasks even though their per-test profiles exist

- Command: `fx-tests task A0wbwr01SLWU1ug1n2B6lw --profiles`
- Expected: the per-test profile URLs (12 `profile_test_*.json` artifacts were uploaded for that task) and the failures from `mochitest-plain_errorsummary.log`.
- Got: "task ... was killed for exceeding its maximum duration ... this tool does not read that format. The job never got to write a profile, so there are no per-test results to read." -- the second sentence is wrong: the per-test failure profiles were uploaded.
- Workaround: list the task's artifacts through the Taskcluster queue API and read the error summary.
