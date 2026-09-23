## Question: the task IDs of passing runs of this test on one config

Wanted a passing `test-windows11-64-25h2-ccov/opt-mochitest-plain` job that ran
`test_group_checkerboarding.html`, to compare its resource-usage profile (test duration, machine
CPU) with the failing one.

Command tried: `fx-tests test <path> --coverage --config windows11-64-25h2-ccov` (counts only),
`fx-tests test <path> --task-ids` (failures only).

Expected: some way to list a few passing task IDs for a config (e.g. `--task-ids --passed`).

Got: no such option. Workaround: Treeherder REST API (`/api/project/mozilla-central/push/?revision=`,
then `/api/jobs/?push_id=`) to list the config's chunks on a push, then
`fx-tests task <id> --passed --limit 0 | rg <test>` on each chunk until one had the test.

## `fx-tests test` verdict "perma-fail" for a test skipped on those configs since a week

Command: `fx-tests test gfx/layers/apz/test/mochitest/test_group_checkerboarding.html`

Got: `Verdict: perma-fail. Never passed on 2 configurations: ...android-em-14-x86_64-ccov...`,
while `--history` shows no failure since 2026-09-14 and the Issues block shows
`SKIP os == 'android' && ccov` 99x (the skip-if landed on 09-14).

Expected: the verdict to say the failures stopped / the configs are now skipped, e.g.
"perma-fail until 2026-09-14, skipped there since".
