## Question: what did the harness say before a bare Android TEST-FAIL?

- Command: `fx-tests test devtools/shared/discovery/tests/xpcshell/test_discovery.js` (and `fx-tests task <id> --profiles`)
- Expected: the failure mode shown as the harness line that preceded the bare `TEST-FAIL`, e.g. `Failed wait for remote log: ... missing?` / `Could not read log file` (2 runs) and `Failed to start process: ... Could not kill left-over process` (1 run).
- Got: `3x FAIL Failure details not recorded (likely Android or platform logging issue)` for all three, so two different harness failures collapsed into one row.
- Workaround: downloaded `public/logs/live_backing.log` for each task and grepped it. The same lines are also INFO/WARNING markers in the resource-usage profile, so the index could pick up the last `remotexpcshelltests.py | ... | <test>` INFO/WARNING line before the TEST-FAIL.

## `--bugs` prints nothing when no bug matches

- Command: `fx-tests test devtools/shared/discovery/tests/xpcshell/test_discovery.js --bugs`
- Expected: a line such as "Bugs: none found naming this test".
- Got: the normal output with no Bugs section, so I could not tell "no bug" apart from "flag ignored".
- Workaround: searched Bugzilla REST by summary.
