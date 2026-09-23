## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/profiles/tests/unit/test_selectable_profile_launch.js --bugs`
- Expected: a line such as `Bugs: none name this test`.
- Got: the usual summary with no bug section at all, indistinguishable from the flag being ignored.
- Workaround: grepped the output, then queried Bugzilla's REST API by summary.

## Question: which configs does one failure mode hit, with counts?

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 1`, piped through awk to strip chunk suffixes and count per config.
- The per-config table mixes all failure modes: here the worst config (macosx1015 debug, 62 fails) is mostly the tree-wide shutdown-hang crash, while the diagnosed `2 == 1` failure is led by linux tsan (20). A per-config count under each issue, or `--issue <n>` filtering the config table, would have answered it.

## `failures --message` does not see crash messages and does not say so

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind that message, or a pointer to `fx-tests crashes`.
- Got: `No failure matched`, although `fx-tests crashes --harness xpcshell` lists it first (19,349 crashes in 503 tests).
- Workaround: `fx-tests crashes`.

## `task --profiles` omits the profile line silently when none was uploaded

- Command: `fx-tests task EzyryVGITPSLqLllitsa6g.0 --profiles` (linux tsan)
- Expected: `no per-test profile uploaded` under the failing test.
- Got: the test's entry with no profile line, which reads like truncated output.
- Workaround: picked another task.
