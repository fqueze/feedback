## Question: "are this test's failures its own, or is it one of hundreds failing in a collapsed job?"

- Command: `fx-tests test devtools/server/tests/xpcshell/test_objectgrips-15.js` (and `--task-ids --limit 0`)
- Expected: some sign that all 6 failing runs are in jobs where 470-1560 other tests also failed (a whole-job collapse), since that changes the diagnosis completely.
- Got: "Verdict: intermittent", `6x TIMEOUT Test exceeded time limit`, and a task-id list. Only running `fx-tests task <id>` on each of the 6 tasks showed "1060 TIMEOUT ... 1061 failing" etc.
- Workaround: ran `fx-tests task` on all 6 tasks.
- Could have shown: per failing run, how many other tests failed in the same job (or a flag like "all 6 failures are in jobs where >30% of tests failed"), and the bug that covers that job-level failure (here bug 1991833, which names no test, so `--bugs` cannot find it).

## Question: "when in the job did the mass failure start, and what was running just before it?"

- Command: `fx-tests task <taskId> --profiles --limit 0`
- Expected: the time of the first failure in the job, and the tests running at that moment.
- Got: ~1000 failing tests listed by manifest, with no times. The harness traceback that explains all of them (`PermissionError: [WinError 5] Access is denied` in `launchProcess`) is an ERROR marker not tied to any test, and does not appear in the output.
- Workaround: `profiler-cli thread markers --search name:test --list --limit 0 --json` on the resource-usage profile, then a script over the JSON to find the first test whose "will retry" line came within 1 s of its start, and the tests overlapping that moment.
- Could have shown: "first failure at t=…, N tests running then: …", plus any harness-level ERROR (traceback) in the job.
