# fx-tests feedback (browser_multiple_ico.js)

## "Which revision did each failure run on?" (to find the landing that stopped a failure)

- Command: `fx-tests test browser/base/content/test/favicons/browser_multiple_ico.js --task-ids --limit 0` (and `--json`)
- Expected: the repo and revision of each failing task, so the last failing push can be bracketed without extra lookups.
- Got: task IDs, job names and days only. The JSON `taskIds[]` entries have no revision either.
- Workaround: one `curl .../queue/v1/task/<id>` per task to read `payload.env.GECKO_HEAD_REV`, then lando's hg2git to map it.

## `--bugs` prints nothing when no bug matches

- Command: `fx-tests test browser/base/content/test/favicons/browser_multiple_ico.js --bugs`
- Expected: a "Bugs: none found" line.
- Got: the normal output with no bugs section, which looks the same as `--bugs` being ignored. I checked Bugzilla by hand to make sure.
