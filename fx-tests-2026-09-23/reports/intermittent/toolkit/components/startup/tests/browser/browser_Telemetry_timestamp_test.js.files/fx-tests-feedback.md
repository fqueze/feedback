## Question: is there a profile of the hung browser for this timeout?

- Command: `fx-tests task HQUaT7-zTdmMOhYBCY_WZQ --profiles` (also ZXTiwKrXTEu-ZKNbsMTfvA, aPu0YQW3RcOAbXe-d5NLfw)
- Expected: the hang profile the harness captured before killing the app ("Attempting to start the profiler to help with diagnosing the hang" -> artifact `public/test_info/profile_0_3866.json`), listed under the timed-out test.
- Got: "No failing test named a per-test profile in this job." The artifact exists in the task's artifact list.
- Workaround: found the `artifact` marker in the resource-usage profile, then listed the task's artifacts with the Taskcluster API.

## Question: which other tests hang the same way ("application timed out after 370.0 seconds with no output") since 2026-09-10?

- Commands: `fx-tests failures --harness mochitest --message "application timed out after 370.0 seconds with no output" --tests --since 12` and `... --message "Test exceeded time limit" ...`
- Expected: the tests behind that timeout, which include this test (`fx-tests test` lists its issue as `TIMEOUT Test exceeded time limit`, 54x) and `browser_browserGlue_client_association_ping.js` (78x).
- Got: "No failure matched" for both messages.
- Workaround: found the sibling test by chance, in `fx-tests task fY0wqB2MSZOmtuByGSgUVQ`.

## Question: which minidump in a job is the parent process of the hung test?

- Command: `fx-tests crash <task> <dump> --thread 0` for each of the 14 `.dmp` artifacts of fY0wqB2MSZOmtuByGSgUVQ
- Expected: `fx-tests task` or `fx-tests test --task-ids` to name the test's parent-process dump (a no-output timeout on Windows leaves one dump per process).
- Got: no dump IDs listed (the crash signature is `[Unknown]`). On CizjR0jPR6qeOIL49LJ7kA, `fx-tests crash` without `--thread` answered "records no crashing thread" for every dump.
- Workaround: looped over every dump with `--thread 0` and grepped for `XRE_InitChildProcess` versus the Glean frame.
