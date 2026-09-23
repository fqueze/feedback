## `fx-tests test` ranks a todo (known-fail) as the "first failure"

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings_restore_from_backup.js`
- Expected: Issues row naming the message that failed the job, `Node is not accessible via accessibility API: id: main-button` (33 of 35 jobs).
- Got: `35x FAIL handleEvent() was unable to perform a11y checks on hidden node`, and `1x Node is not accessible`. The hidden-node message is an `a11yWarn` (`SimpleTest.todo`), and the profile marks it `TEST-KNOWN-FAIL`. In the first run of a rerun-mode job the log prints every buffered message as `TEST-FAIL`, todos too, so the log alone cannot tell them apart. The same goes for the `changed preference:` todos.
- Workaround: ran `fx-tests task <id> --messages` on all 35 jobs, dropped the hidden-node and changed-preference lines, and counted what was left.

## The failing subtest and line of each failing execution

- Question: in which subtest, and at which line of the test, did each failing execution fail? The failure moves between 5 click sites here.
- Command: none answers it. I downloaded all 35 `live_backing.log`s and parsed the stack under each `Node is not accessible` line.
- What could show it: `fx-tests test --task-ids` could print the subtest name (it is in the message prefix) and the top test-file frame of the stack next to each task.

## `fx-tests test --bugs` prints nothing

- Command: `COLUMNS=250 fx-tests test browser/components/backup/tests/browser/browser_settings_restore_from_backup.js --bugs`, with stderr kept.
- Expected: a bugs section, or a line saying that no bug names the test. Bugzilla has 4 bugs whose summary names it, all resolved: 1988624 (the single tracking bug, RESOLVED INCOMPLETE 2026-08-24), 2019974, 2022362, 2022413.
- Got: the same output as without `--bugs`, and nothing on stderr either. "No bug" and "the lookup did not run" look the same.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_settings_restore_from_backup"`.
