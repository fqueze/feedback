## Question: "the failing tasks of this test on macosx1500-aarch64-vms, and their per-test profiles"

- Command: `fx-tests test browser/extensions/formautofill/test/mochitest/test_autofill_and_ordinal_forms.html --coverage` (and `--config aarch64-vms --history`)
- Expected: the 26 macosx1500-aarch64-vms jobs sheriffs starred on bug 1803644 (2026-09-17..21, from `fx-tests intermittent --bug 1803644`) counted as timeouts.
- Got: `test-macosx1500-aarch64-vms/opt-mochitest-plain  205 runs 205 pass 0 fail` — the config reads as healthy. Every one of those jobs hit the 3600 s max run time, so the tool drops them entirely. The only trace is a drop in daily pass counts in `--history`.
- Then: `fx-tests task c5kEtvUAS3ioWjPiUNhfcA --profiles` -> "killed for exceeding its maximum duration ... no per-test results to read". But the task's artifacts include `profile_test_autofill_and_ordinal_forms.html.json` and `-2.html.json` (plus 6 other per-test profiles) and `mochitest-plain_errorsummary.log`, which name every failure.
- Workaround: listed the artifacts with the Taskcluster queue API and read the errorsummary by hand.
- What would have helped: for a killed task, still list the per-test profile artifacts and the errorsummary failures. And in `test`, count killed jobs (or at least flag "N jobs of this config were killed at max run time, not counted").
