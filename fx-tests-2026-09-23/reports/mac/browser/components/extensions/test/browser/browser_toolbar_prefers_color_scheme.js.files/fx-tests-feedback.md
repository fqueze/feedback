## Question: were all these failures on revisions before a given fix landed?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_toolbar_prefers_color_scheme.js --task-ids --limit 0` (also with `--json`)
- Expected: the repo and revision (ideally the push time) of each failing task, so failures can be placed before or after a landing. The three runs dated 2026-09-14 were on revisions pushed 2026-08-15, which looks like a recurrence after the fix until you check.
- Got: task id, job name, day of the run only; no revision in text or JSON.
- Workaround: one `fx-tests task <id>` per task to read the revision, then `https://hg.mozilla.org/<repo>/json-pushes?changeset=<rev>` for the push date.

## Question: which test failed first in each job that this victim failed in?

- Command: same as above, then 31 runs of `fx-tests task <id> --messages --limit 0`, grepping each for the suspected leaker's message.
- Expected: for a test whose failures are a cascade (the same message in 40+ tests of one job), the first failing test of each job, or the tests that failed alongside it, from `test --task-ids`.
- Got: nothing about the rest of the job; one `task` call per job to find out.
- Workaround: shell loop over the 31 task ids.
