## Which occurrences of one failure mode have a per-test profile?

- Command: `fx-tests test browser/components/newtab/test/xpcshell/test_nimbus_newtabTrainhopAddon.js --task-ids --limit 0 --issue 5`, then `fx-tests task <id> --profiles` on 6 of the 123 tasks.
- Expected: a way to see, for one issue, which tasks have a per-test profile of the test, or a note in `task --profiles` saying why a failing test has none.
- Got: `task --profiles` lists no `profile` line under the failing test and says nothing about it. I had to query the Taskcluster artifact list of all 123 tasks with curl to learn that none has one (the failure is only a cleanup error, and head.js does not upload a profile when only cleanup functions fail, since `_passed` stays true).
- Workaround: curl `.../runs/<n>/artifacts?limit=1000` per task. `--profiles` on `fx-tests test ... --issue <n>` listing the profile URLs, or "no per-test profile" per task, would have answered it.

## Is this failure mode parallel-only?

- Command: `fx-tests test <path> --executions --issue 5`
- Expected: the parallel/sequential breakdown for issue 5 only.
- Got: `fx-tests: --issue selects which failure the printed task IDs belong to, so it needs --task-ids`. `--executions` alone mixes all 13 failure modes, and this test's timeouts dominate it.
- Workaround: read "Failed only in the parallel phase" / "passed when the harness reran them" in `fx-tests task` output of a few tasks.
