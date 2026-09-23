## Question: "what did my test get in this job" when the job has hundreds of failures

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: a way to see the row for one test (e.g. `--test <path>`), since I arrived from `fx-tests test <path> --task-ids`.
- Got: the first 20 of 488 failing tests, alphabetically; my test was not among them.
- Workaround: `COLUMNS=250 fx-tests task <id> --profiles --limit 0 --passed --full-messages > file` for all six jobs, then grep. That is ~2000-6700 lines per job to answer a one-row question.
- Also useful here: `fx-tests test <path> --task-ids` could flag jobs where the test is one of hundreds of failures (a mass-failure job), since that changes the diagnosis entirely.
