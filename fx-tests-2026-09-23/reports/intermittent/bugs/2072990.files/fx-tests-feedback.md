## Question: "which failure modes are behind the jobs starred on this bug?" (non-test job)
- Command: `fx-tests intermittent --bug 2072990 --limit 0`
- Expected: the failure line per occurrence, so the modes can be told apart (here three: 503 on a commit query, 503 misreported as tree drift, 503 on gfx/angle, plus a max-run-time abort).
- Got: `Tests named, per annotated job: (none: ... the API only keeps lines matching that marker)` and `lines: []` in `--json` for every occurrence. The job prints `TEST-UNEXPECTED-ERROR | <moz.yaml path> | ...`, which names a path but is not kept.
- Workaround: downloaded all 16 live_backing.log files and grepped them.

## Question: "how many runs of this job failed, and how many passed, over the window?" (source-test job)
- Command: none available; `fx-tests test third_party/zucchini/chromium/moz.yaml` exits 2 (only xpcshell/mochitest data).
- Expected: runs vs failures per job name (`source-test-vendor-verify-algorithms`, `-gfx`) and per day, since sheriffs starred only 16 of 54 failures on the bug (35 went to "expected fail").
- Workaround: Treeherder `/api/project/<repo>/jobs/?job_type_name=...` plus a Python script over the JSON.

## `fx-tests task <taskId> --profiles` on a non-test job
- Command: `fx-tests task VXy2E9JJQj26P0IT5zo0eg --profiles`
- Got: "has no profile_resource-usage.json ... Taskcluster expires task artifacts after about a month, so this is permanent". The task is 5 days old; the real reason is the last sentence ("A job that is not a test job never uploads one"). Leading with expiry is misleading when the task is recent; exit code was 0, not 4.
- Correction to the above: the 'exit code was 0' was measured through a pipe to head, so it is not the tool's exit code; disregard it.
