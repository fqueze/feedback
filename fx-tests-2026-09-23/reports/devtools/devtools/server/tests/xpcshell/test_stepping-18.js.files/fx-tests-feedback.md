## `failures --message` does not find a crash signature that `test` lists under Issues

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests --limit 0`
- Expected: the tests failing with this message, since `fx-tests test <path>` lists it under Issues as `CRASH child process hang at shutdown`.
- Got: "No failure matched", with advice to check for typos.
- Workaround: `fx-tests crashes --harness xpcshell --signature "hang at shutdown"`. A hint in the no-match message ("this is a crash signature, see `fx-tests crashes`") would have saved the call.

## Question: are a test's failures concentrated on a few workers?

- Command: none in fx-tests; I ran `curl .../api/queue/v1/task/<id>/status` for each of the 105 failing task IDs from `fx-tests test <path> --task-ids --limit 0` and counted `runs[0].workerId`.
- What could show it: a worker column in `--task-ids`, or a per-worker count for the failing config (failures and runs per worker). It tells a bad worker from a pool-wide environment problem.

## Question: which minidump IDs belong to this test (to sample several)

- Command: `fx-tests test <path> --task-ids --limit 0`, then `rg "fx-tests crash" | awk` to get the `<task> <dump>` pairs.
- Fine as is, but a `--minidumps` flag on `test` that prints only the pairs, one per line, would make sampling dumps a one-liner.
