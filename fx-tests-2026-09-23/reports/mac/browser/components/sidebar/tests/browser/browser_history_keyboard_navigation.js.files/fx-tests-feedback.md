## Question: are a day's failures on current revisions, or retriggers of old ones?

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_history_keyboard_navigation.js --task-ids --limit 0`
- Expected: the revision (and its push date) next to each task ID, or a note that the 28 failures dated 2026-09-14 ran on revisions pushed a month earlier.
- Got: task IDs and job names only. `--history` shows a one-day spike on 2026-09-14 that reads like a regression that was backed out.
- Workaround: `fx-tests task <id>` on each of the 28 tasks for the revision, then `curl https://hg.mozilla.org/integration/autoland/json-pushes?changeset=<rev>` for the push dates: all 28 are pushes 275391–275407 (2026-08-14/15). The output could show the revision per task, and flag failures whose revision's push predates the run by days.

## Chunk labels disagree between `test --task-ids` and `task`

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task SqjE3rcdTyyg9fn2n0K1hg` and `fx-tests task BMFOuIyvTPqzBQnP3eMo7g`
- Expected: the same job name in both.
- Got: `test --task-ids` lists SqjE3rcdTyyg9fn2n0K1hg as `test-macosx1500-aarch64/opt-mochitest-browser-chrome-1` and BMFOuIyvTPqzBQnP3eMo7g as `test-macosx1500-aarch64/opt-mochitest-browser-chrome` (no chunk); `task` names both `...-chrome-7`, as it does all 28 tasks.
- Workaround: trusted `fx-tests task`.

## `--bugs` prints nothing when it finds nothing

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_history_keyboard_navigation.js --bugs`
- Expected: a Bugs section, or a line saying no sheriff-annotated bug names the test in the window.
- Got: the same output as without `--bugs`; `--json` has `annotatedBugs: []`. Bugzilla has bug 2059524, the single tracking bug for this exact failure (resolved INCOMPLETE on 2026-09-16).
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<test file name>`.
