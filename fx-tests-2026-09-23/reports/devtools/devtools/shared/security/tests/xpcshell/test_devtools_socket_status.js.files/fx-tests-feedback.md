## `fx-tests task` does not say a job broke wholesale, nor why

- Command: `fx-tests task Q6pDc4X0SYOXqgi4KIpuzA --profiles`
- Expected: for a job where 476 of 772 tests TIMEOUT within the same second, a line saying the job broke wholesale, with the harness exception that ended it (`Following exceptions were raised: ... PermissionError: [WinError 5] Access is denied` from `_winapi.CreateProcess`, present both in the log and as an ERROR marker in the resource-usage profile).
- Got: 20 per-test TIMEOUT rows ("Test timed out", "force-killed by the harness"), which read as 476 independent timeouts.
- Workaround: downloaded `live_backing.log` and grepped for `Traceback`; also bucketed the resource profile's `test` markers by start time with a script to see that every test started after t=45.7s failed within 10 ms.
- Same question for `fx-tests test <path>`: "is each failure of this test one of those job-wide breakages?" — all 3 TIMEOUTs of this test were, and the per-config table cannot say so.

## `fx-tests test --task-ids` does not pair each task with its failure mode

- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: each task ID tagged with the issue number it hit.
- Got: a flat list; had to rerun with `--issue 1`, `--issue 2`, `--issue 3`.
