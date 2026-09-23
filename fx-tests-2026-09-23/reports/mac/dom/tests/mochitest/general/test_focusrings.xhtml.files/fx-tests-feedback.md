## `fx-tests test` files a confirm-failure job's known message as "Failure details not recorded"

- Command: `fx-tests test dom/tests/mochitest/general/test_focusrings.xhtml`
- Expected: the 6 failures of the `-cf` job W9Td9a0sSye6v51ABuaF7w under their message, `[SimpleTest.finish()] No checks actually run...`.
- Got: `6x FAIL Failure details not recorded (likely Android or platform logging issue)` as the top issue, and only 2x `No checks actually run`. `fx-tests task W9Td9a0sSye6v51ABuaF7w --messages` shows all 6 executions with `No checks actually run`. The most frequent failure mode reads as unknown.
- Workaround: `fx-tests task <id> --messages` on each job.

## `fx-tests test --task-ids` lists one job twice

- Command: `fx-tests test dom/tests/mochitest/general/test_focusrings.xhtml --task-ids --limit 0`
- Got: `W9Td9a0sSye6v51ABuaF7w.0 ... -cf ×2` and `W9Td9a0sSye6v51ABuaF7w.0 ... -cf ×4` as two rows under 2026-09-20, while the header says "3 jobs". Looks like one row per recorded message, with nothing saying so.

## `fx-tests task --profiles` misses the last per-test profile of a `--repeat` job

- Command: `fx-tests task W9Td9a0sSye6v51ABuaF7w --profiles`
- Expected: 6 profiles for "6 failing executions of 6".
- Got: 5 (`profile_test_focusrings.xhtml.json` to `-5`). The job's log says `profile uploaded in profile_test_focusrings-6.xhtml.json`, and that artifact exists (HTTP 200).
- Workaround: read the `FAIL ... profile uploaded in ...` markers in the resource-usage profile.

## `fx-tests test --bugs` prints nothing about bugs when none is found

- Command: `fx-tests test dom/tests/mochitest/general/test_focusrings.xhtml --bugs`
- Got: the normal summary, no "Bugs" section and no "no bug found" line, so I could not tell "none" from "flag ignored". Checked Bugzilla by hand.
