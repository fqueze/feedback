# fx-tests feedback — test_DominatorTreeNode_LabelAndShallowSize_01.js

## Android FAIL reported as "Failure details not recorded" when the harness did say why

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTreeNode_LabelAndShallowSize_01.js`
  and `fx-tests task c9NatOk1Tgq5LJ9nTwF8VA --profiles`
- Expected: the failure mode named from the job, e.g.
  `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)` in `test`,
  and `FAIL — 1 failing execution of 2` with no message in `task`.
- The job's resource-usage profile has that line as an INFO marker naming the test, at the
  moment of the failure. Picking up `remotexpcshelltests.py | Failed to start process:` INFO
  lines as the failure message (when no TEST-UNEXPECTED line exists) would have answered
  "why did it fail" without loading the profile. It also hit intl/uconv/tests/unit/test_decode_8859-14.js
  in the same job.
- Workaround: loaded the resource-usage profile and searched markers for the test name.

## `failures --message` does not find the text `test` shows under Issues

- Command: `fx-tests failures --harness xpcshell --message "Failure details not recorded" --tests`
- Expected: the tests failing without a recorded message, since `fx-tests test` prints that
  text as the Issue.
- Got: "No failure matched". The same rows are under `(no message recorded)` in `failures`.
- Workaround: none needed here; the two commands just word the placeholder differently.
