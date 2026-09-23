## `--task-ids --limit 0` prints a second, unlabelled block of dates

- Command: `COLUMNS=250 fx-tests test toolkit/mozapps/update/tests/unit_aus_update/initialStateValidation.js --task-ids --limit 0`
- Expected: one list of failing tasks, or one block per failure mode with the mode named.
- Got: a `Task IDs` block of dates 2026-08-31 … 2026-09-20, then, with no header, a second block that starts again at 2026-08-31 with other configs (macosx1015, windows, linux). It looks like the FAIL tasks after the TIMEOUT ones, but nothing says so, and `tail` on the output shows the wrong block as "the most recent".
- Workaround: `--issue <n>` per failure mode.

## `--bugs` prints nothing when there is no bug

- Command: `COLUMNS=250 fx-tests test toolkit/mozapps/update/tests/unit_aus_update/initialStateValidation.js --bugs`
- Expected: a `Bugs: none found` line.
- Got: the same output as without `--bugs`; `--json` has `annotatedBugs: []`. Without a line it reads as the flag being ignored.
- Workaround: `--json`, and a Bugzilla quicksearch.
