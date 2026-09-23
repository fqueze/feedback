## Question: "the tests behind this crash signature"

- Command: `fx-tests crashes --signature NS_DispatchToMainThread --limit 0`
- Expected: the tests that hit the signature, with counts.
- Got: one row `558  12  0  @ NS_DispatchToMainThread` (12 tests), without naming them.
- Workaround: `--json` and read `rows[].tests[]`.
- What could have shown it: with `--signature` narrowing to few rows, list the tests under each row.

## Question: "what was an Android xpcshell test doing when it timed out"

- Command: `fx-tests task W5OAYjCqSrGtpZbqnyyiUg.0 --profiles`
- Expected: some pointer to where the test's own log is.
- Got: `No failing test named a per-test profile in this job.`; the resource-usage profile's "full log" replay for the test is empty.
- Workaround: the job's `public/test_info/logcat-emulator-5554.log` artifact (≈250 MB) holds the whole structured test log (`I Gecko : {"action":...}` lines per pid); grep by the xpcshell pid found via `XpcshellTestRunner: Got extras arg24=... _TEST_NAME`.
- What could have shown it: on Android jobs, name the logcat artifact (and the pid of each execution) next to the failure.

## Question: "a recent job where this test passed on this config" (to compare with a failing one)

- Command: `fx-tests test <path> --task-ids --config <cfg>` lists failing tasks only.
- Workaround: Treeherder API (`/api/project/autoland/jobs/?job_type_name=...&result=success`), then grep the job log to check the test ran.

## "Passed when the harness reran it" can be a killed run on Android

- `fx-tests task W5OAYjCqSrGtpZbqnyyiUg.0` reports the test as passing on rerun. The passing `xpcshell.toml` rerun (`took 10308ms`) was force-killed after 8 s by the previous timed-out test's process monitor (`xpcshell-remote.toml ... | 18731 | Timing out`, 18731 being the rerun's pid), and its logcat shows it stalled in the same place as the failing runs. The harness counted it as a pass. A rerun "pass" much shorter than the config's median is worth flagging.
