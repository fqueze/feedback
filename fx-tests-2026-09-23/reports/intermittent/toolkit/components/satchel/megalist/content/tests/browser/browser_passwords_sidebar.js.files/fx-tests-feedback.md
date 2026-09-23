## Question: what was the failure rate per config before a fix landed, and after?

- Command: `fx-tests test <path> --since 21 --coverage --limit 0 --json` and the same with `--since 13`, then a script subtracting the two per `jobName`.
- Expected: a way to bound the window at both ends (`--until <date>` / `--before <date>`), so the pre-step rate of a test that stopped failing reads directly.
- Got: `--since` only trims the start, so the default table mixes 8 failing days with 13 clean ones (msix shows 21.6% where the pre-step rate was 37.0%).
- Workaround: two JSON dumps and a subtraction.

## Question: which revision (and tree, push time) is behind each failing task, to bracket the landing that stopped it?

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 2`, then one `curl https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>` per task to read `GECKO_HEAD_REPOSITORY` / `GECKO_HEAD_REV`.
- Expected: the revision and tree next to each task id (the data has it: `fx-tests task` prints it).
- Got: task ids grouped by day only.
- Workaround: 25 Taskcluster queries for the last failing day.

## `intermittent --bug` default hides that a trunk-fixed failure still happens on beta/esr

- Command: `fx-tests intermittent --bug 1927955` — exit 2, "no sheriff annotations ... on trunk between 2026-09-16 and 2026-09-22".
- Expected/got: correct, but for a failure fixed on trunk the interesting part is that it is still annotated on mozilla-esr153 and mozilla-beta; only `--since 21 --tree all` showed that.
- Workaround: `--since 21 --tree all`. The hint in the error message pointed there; noting it because the default reads like "nothing".
