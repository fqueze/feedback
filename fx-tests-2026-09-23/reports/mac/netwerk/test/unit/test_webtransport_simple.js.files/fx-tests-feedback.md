## Question: "where in the test did each timed-out run stop?"

- Command: `fx-tests test netwerk/test/unit/test_webtransport_simple.js --task-ids --limit 0 --issue 5`, then `fx-tests task <id>` for each.
- Expected: for a TIMEOUT, the last subtest started (`Starting <task>`) and the last log line before the timeout, per run, so that "always the same step" can be told apart from "different steps" without opening logs.
- Got: task IDs and "Test timed out" only. For an xpcshell timeout the replayed "full log" is the only record of where the test was, and it is in `live_backing.log`.
- Workaround: downloaded 18 `live_backing.log` files with curl and extracted the `Begin/End of full log` block with awk (last `Starting …` line, whether `exiting test` appears, last INFO line). That showed three distinct endings: finished after the dump, `onSessionClosed(false)`, and stopped mid-task.

## Question: "what was the harness timeout on this config?"

- Command: none available.
- Expected: `fx-tests test <path> --durations` or `fx-tests task` to show the effective per-test timeout (`Using harness timeout of 180s (base=30s, factor=6.0)` on macosx1015 vs 30s on macosx1500). It changes how a max duration of 39 s on macosx1015 reads.
- Got: durations only.
- Workaround: grepped `Using harness timeout` in the job log.
