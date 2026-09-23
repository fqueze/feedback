## Question: a job of the worst config where this test passed, to compare with a failing one

- Command: `fx-tests test <path> --task-ids --limit 0` (and `--profiles`).
- Expected: a way to list a few task IDs where the test ran and passed on a given config (e.g. `--task-ids --status pass --config standalone`), since for a shutdown leak the only comparison available is the resource-usage profile of a passing job.
- Got: failing tasks only.
- Workaround: Treeherder REST (`/api/project/autoland/jobs/?job_type_name=…&last_modified__gt=…`), then `fx-tests task <id> --json` on each candidate and a script searching for the test's `statuses` to find ones where it was `PASS`.
- What the output could have shown: passing task IDs per config, or in `fx-tests task`, the status of one named test (`fx-tests task <id> --test <path>`).
