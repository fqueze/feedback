## Question: "which tasks ran this test on the try push, when it passed everywhere"

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/inspector/rules/test/browser_rules_selector_warnings.js --all-jobs --task-ids --profiles --limit 0` (and the same with `--json`)
- Expected: the task IDs (and resource-usage profile URLs) of the job runs counted in each config row, so a passing run can be opened with `fx-tests task` / `profiler-cli`.
- Got: only the per-config counts table; `--task-ids` and `--profiles` print nothing in `--test` mode, and the JSON has no task IDs either.
- Workaround: Treeherder REST API (`/api/project/try/push/?revision=…`, then `/api/jobs/?push_id=…`), filtered by the job names from the table.

## Question: "which bug is this test's skip-if about"

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_selector_warnings.js --bugs`
- Expected: Bug 1945519, which the removed skip-if's comment names and whose summary names the test.
- Got: the same output as without `--bugs`, no bug section.
- Workaround: read the skip-if comment in the manifest diff.
