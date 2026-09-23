## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/client/responsive/test/browser/browser_touch_all_events_long_tap.js --bugs`
- Expected: a line saying no sheriff-annotated bug names this test (e.g. "Bugs: none").
- Got: the same output as without `--bugs`, with no bug section at all, so "no bug" looks the same as "flag ignored" or "query failed silently".
- Workaround: `--json` and read `annotatedBugs` (it is `[]`).

## `fx-tests test` "Issues (first failure per run)" counts a secondary message as a separate failure mode

- Question: are the 2 runs listed under the a11y-check message (`handleEvent() was unable to perform a11y checks on hidden node: id: touch-simulation-button ...`) a different failure, or the same one with an extra message?
- Command: `fx-tests test devtools/client/responsive/test/browser/browser_touch_all_events_long_tap.js` (issue 5), then `fx-tests task <id> --messages` for each of its 2 tasks.
- Expected: the issue row to say the runs also contain issue 4's message, e.g. "2x ... (both also have issue 4)".
- Got: 23x touchend timeout and 2x a11y message, which reads as two failure modes. `fx-tests task FNBcPEWFS2SPtgbRtoMYhg --messages` and `fx-tests task KjNKDyIuRUSD4kjgJllB0g --messages` show that both runs also have the touchend timeout, so all 25 runs have it.
- Workaround: `fx-tests task <id> --messages` for each task of the minor issue.
