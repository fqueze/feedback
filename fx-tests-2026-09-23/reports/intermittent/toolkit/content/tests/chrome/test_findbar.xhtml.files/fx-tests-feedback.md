## Question: were all the failures on revisions older than the fix's landing?
- Command: `fx-tests test toolkit/content/tests/chrome/test_findbar.xhtml --task-ids --limit 0 --day 2026-09-08` (and `--json`)
- Expected: each failing task's revision (and push time or task creation time), so that "every failure predates push X" can be read off directly.
- Got: task IDs, job names and day only. `--json` has `taskId`, `day`, `jobName`, `message`, and no revision or time.
- Workaround: I fetched `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>` for each of the 26 tasks to get `created` and `payload.env.GECKO_HEAD_REV`, then compared them with the push time from hg json-pushes.
- The output could show a revision and a creation time per task row, or a "last failure at <time> on <rev>" line in `--history`.

## `--since` takes days, not a date
- Command: `fx-tests test <path> --task-ids --since 2026-09-08`
- Expected: failures from that date on, as `--day` accepts a date.
- Got: `--since expects a non-negative integer, got "2026-09-08"`.
- Workaround: `--day 2026-09-08`. Accepting a date as well would help, because the question is often "since the landing on <date>".
