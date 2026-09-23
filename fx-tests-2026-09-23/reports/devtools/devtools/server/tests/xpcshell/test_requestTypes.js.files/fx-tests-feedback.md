## Question: "what did this one test do in this job"

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles | rg test_requestTypes`
- Expected: the entry for devtools/server/tests/xpcshell/test_requestTypes.js (status, message, profile if any).
- Got: nothing; the FAILED list is truncated to 20 of 1061 entries, so the test was cut. Only `--limit 0` (1000+ entries) found it.
- Workaround: `--limit 0` then grep.
- Could have shown: a `--test <path>` filter on `fx-tests task`, returning that test's executions only.
