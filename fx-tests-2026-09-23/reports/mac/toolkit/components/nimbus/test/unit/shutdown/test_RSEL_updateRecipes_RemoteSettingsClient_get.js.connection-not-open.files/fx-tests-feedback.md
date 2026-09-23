## Which configs does one failure message hit, across every test that has it?

- Commands: `fx-tests failures --harness xpcshell --message "Connection is not open" --tests --limit 0` (27 tests, 528 failures), then `fx-tests test <path> --task-ids --limit 0 --json` for each of the 27 tests and a Python script filtering `taskIds[]` on `message` and counting `jobName`.
- Question: "is `uncaught rejection: Connection is not open.` macOS-only across all 27 tests?" (it is: 528 of 528). That decided whether a Darwin-only code path could be the cause.
- Expected: `fx-tests failures --message <text>` to give a per-config (or per-platform) breakdown of the message's failures. It says "This file records no job names, so nothing here can be broken down by configuration" and points nowhere.
- What its output could have shown: a `--configs` option on `failures --message` that does the per-test join itself (the per-test files do carry `jobName`), or at least a hint to use `fx-tests test --issue` per listed test.

## The failure's own stack is only in the job log

- Commands: `fx-tests task <taskId> --profiles`, then `curl .../public/logs/live_backing.log` and `grep -n` for the TEST-FAIL line, and `sed` for the 10 lines after it (done for all 26 failing tasks).
- Question: "which code created the rejected promise?" The xpcshell harness prints the rejection's JS stack right after `TEST-FAIL ... uncaught rejection: ...`, but `fx-tests task` and `fx-tests test --task-ids` show only the one-line message, and the profile's `TEST-UNEXPECTED-FAIL` marker only carries the stack of `assertNoUncaughtRejections`.
- What its output could have shown: the stack lines that follow the failure message in the log (e.g. `fx-tests task <id> --stack`, or the first frames in `--task-ids` rows), so that "same stack in all 26 failures" is one command.
