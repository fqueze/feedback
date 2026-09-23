## `test` Issues names a TEST-KNOWN-FAIL as the failure

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_stylesheet_text.js`
- Expected: the Issues line to show the message that made the runs fail, `TEST-UNEXPECTED-FAIL | The new stylesheet text is correct for '<subtest>' - Got "... name: ;" ...` (three subtest variants, 19/12/9 runs).
- Got: `41x FAIL handleEvent() was unable to perform a11y checks on hidden node: ... ruleview-selectors-container`. In the profile that message is `TEST-KNOWN-FAIL` (a11y checks are todo in this job) and is logged in every run, passing or not. `--issue`/`--task-ids` group all 41 runs under it, so the real failure modes are not separable.
- Workaround: `fx-tests task <id> --messages` on each of the 40 tasks, dropping the a11y lines, and tallying by hand.

## The question: which failure message, per failing run, across all runs of one test

- Needed a loop over `fx-tests task <id> --messages` for 40 task ids, grep-filtered, to answer "which subtest's assertion failed in each run". `fx-tests test <path> --issues` (or `--messages`) listing every distinct unexpected-fail message with counts would have answered it in one call.
