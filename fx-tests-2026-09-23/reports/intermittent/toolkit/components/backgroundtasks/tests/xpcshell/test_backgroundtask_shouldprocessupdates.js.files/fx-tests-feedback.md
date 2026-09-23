## Question: "in the jobs where test A failed with status X, did test B run, and did it pass?"

- Command: `fx-tests test toolkit/crashreporter/test/unit/test_crashreporter_crash.js --task-ids --limit 0`, then one `fx-tests task <id> --passed --limit 0` per listed job (17 calls, plus the same for test_crash_terminator.js samples).
- Expected: a way to tell, from the `--task-ids` list, which status each job's failure had (here `CRASH, TIMEOUT` vs plain `TIMEOUT`: the discriminator for the leaker) and whether a second test ran in the same job. For example `fx-tests test <A> --task-ids --with <B>` printing B's outcome in each of A's failing jobs (fail / pass / not in this chunk).
- Got: `--task-ids` lists only the task and config; the per-job status (`CRASH, TIMEOUT — 2 failing executions of 3`) only appears in `fx-tests task`, and "did B run in this job" needs `--passed --limit 0` and a grep, per job.
- Workaround: shell loop over `fx-tests task`, grepping each output.
- Also: `fx-tests test` "Issues (first failure per run)" shows only `TIMEOUT` for these crash tests, hiding that 22 of their timeouts also reported a CRASH (the harness's minidump), which is what separates the leaking runs from the harmless ones.
