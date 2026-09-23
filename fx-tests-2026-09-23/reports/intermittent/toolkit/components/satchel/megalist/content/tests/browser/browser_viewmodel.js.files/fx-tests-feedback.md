## Question: which push did this test first fail on?

- Command: `fx-tests test toolkit/components/satchel/megalist/content/tests/browser/browser_viewmodel.js --task-ids --day 2026-09-15` (and `--json`)
- Expected: next to each failing task, its tree and revision (or push time), so the first failing push after a `--history` step change can be read directly, to look for the regressor.
- Got: task ids, job names and days only; `--json` `taskIds[]` has no revision either.
- Workaround: one `curl https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>` per task to read `payload.env.GECKO_HEAD_REV`, then hg2git mapping to read `git log`.
