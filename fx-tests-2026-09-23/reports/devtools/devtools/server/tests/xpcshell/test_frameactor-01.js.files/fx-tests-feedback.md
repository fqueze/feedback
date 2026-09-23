## Finding this test in a 488-failure job needed `--limit 0` and a grep

- Question: what did this one test do in this job, and does it have a per-test profile?
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles` (truncated to the first ~20 failures, alphabetical), then `--limit 0` saved to a file and grepped.
- Expected: a way to ask about one test in a job (e.g. `--test <path>`), or the job's failure-mode summary first ("487 TIMEOUT started within 2 s").
- Got: 1,800+ lines, one block per failing test.
- Workaround: `--limit 0 > file`, then `rg -A3 <test name>`.
