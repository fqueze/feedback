## `--bugs` is silent when no bug is annotated
- Command: `fx-tests test netwerk/test/unit/test_trr_confirmation.js --bugs`
- Expected: a line saying no sheriff-annotated bug names this test.
- Got: the normal output with no bugs section at all, indistinguishable from the flag being ignored.
- Workaround: `--bugs --json` and read `annotatedBugs: []`.

## "Failure details not recorded" hides a message that is in the job log
- Question: why did this test fail on Android (118 runs in Issues row "Failure details not recorded (likely Android or platform logging issue)")?
- Commands: `fx-tests test <path> --task-ids --issue 2`, then `fx-tests task M6lMOcAaRFOCt8f-p8xmLw --profiles --messages` (no message, no per-test profile).
- Workaround: downloaded `public/logs/live_backing.log` and grepped: in 4 of 4 tasks the line just before `TEST-FAIL | netwerk/test/unit/test_trr_confirmation.js | took ~3000ms` is `remotexpcshelltests.py | Failed to start process: Only one instance of an application may be running at once`, i.e. the test never started. The 21 Android TIMEOUTs are the same shape: `Launched Test App`, no output, force-killed after 122 s.
- What could have shown it: the `remotexpcshelltests.py | Failed to start process: ...` line as the failure message, so this row groups with the harness failure instead of with the test.

## `--issue <n>` numbering follows the other filters
- Command: `fx-tests test <path> --task-ids --issue 3 --since 3` returned the "Failure details not recorded" tasks, where row 3 without `--since` is `confirm_start_in_failed_state`.
- Expected: either the same numbering, or the row's message echoed prominently (it is echoed in the header, which I missed).
- Workaround: re-ran without `--since`.
