## Question: "did this non-test CI job (a lint job) pass or fail on each push between the regressor and the fix?"

- Command: `fx-tests intermittent --bug 2071221 --since 21 --tree all --limit 0`
- Expected: some way to see the job's outcome per push over a window, including the green runs before and after, so that "perma from push X to push Y" can be shown.
- Got: only the sheriff-annotated failures, and "Tests named: none" because a lint job has no TEST-UNEXPECTED-FAIL test. There is no fx-tests command for a job that is not a test (`fx-tests test` needs a test path).
- Workaround: a Python script over the Treeherder REST API (`/api/project/autoland/push/?push_timestamp__gte=...` then `/api/jobs/?push_id=...&job_type_name=source-test-mozlint-typescript`), which took about 450 push queries.
- What would have shown it: a `fx-tests job <job-type-name> --since <date>` that prints the result per push (first failure, last failure, first green after).
