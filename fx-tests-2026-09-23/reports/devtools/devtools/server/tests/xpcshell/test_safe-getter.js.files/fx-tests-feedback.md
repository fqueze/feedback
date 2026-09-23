## Question: which child process hung, for a `child process hang at shutdown` crash? (macOS)

- Command: `fx-tests crash Mb0qnwpCTCCGHAkuohIC0Q.0 A156846A-F381-44C6-91F1-B58BFCF8F3B2 --raw` (read `pid`, `process_uptime`)
- Expected: the hung child's pid and uptime, matching `Process 10925 hanging at shutdown` in the job's log.
- Got: pid 10903, uptime 14 s. On macOS out-of-process dumps, breakpad's `MinidumpGenerator::WriteMiscInfoStream` writes `getpid()` of the process writing the dump (the crash helper), so both values belong to the writer. I spent a while chasing a phantom "second child".
- Workaround: took the pid from the ProcessWatcher C++ warning in the resource-usage profile.
- Could show: a note in `fx-tests crash` output that pid/uptime are unreliable for mac out-of-process dumps, or the hung child's pid from the `Process N hanging at shutdown` log line.

## Question: are a test's failures concentrated on some workers?

- Command: `fx-tests test <path> --task-ids --limit 0`, then `curl .../task/<id>/status` for each of the 111 tasks to get `workerId`.
- Expected: the worker next to each task ID.
- Got: task IDs only.
- Could show: `workerId` per task, and a per-worker count. Here it was the decisive signal (111 failures on 22 of 77 workers).

## Question: a passing run of this test on the failing config, to compare with

- Command: none in fx-tests; walked Treeherder `/api/jobs/?push_id=` for recent pushes, then `fx-tests task <id> --passed --limit 0 | grep <test>` per job.
- Expected: `fx-tests test <path> --config <c>` listing some passing task IDs.
- Got: only failing task IDs.

## `failures --message` does not find crash messages

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests with that message, or a pointer to `crashes`.
- Got: `No failure matched`, although `fx-tests crashes` lists that signature 19,591 times over 507 tests.
