## Question: are this test's failures its own, or collateral of jobs where everything failed?

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_fetch-bom.js --task-ids --limit 0`, then `fx-tests task <id>` for each task.
- Expected: `fx-tests test` to flag that each of the 3 failing jobs had 755 to 1564 failing tests (most of the job), e.g. a per-task "N of M tests failed in this job" column or a "job-wide breakage" note next to the verdict. That alone says "not this test".
- Got: the verdict said "intermittent, 1.2% on msix". I only saw the job-wide failure after running `fx-tests task` on each ID.
- Workaround: ran `fx-tests task <id> | head` for each task.
