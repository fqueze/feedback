## Question: is there a bug for this test?

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_run_search_back_navigation.js --bugs`
- Expected: a Bugs section, or an explicit "no sheriff-annotated bug names this test".
- Got: the ordinary `fx-tests test` output with no Bugs section at all; `--bugs --json` has no `bugs` key. Cannot tell "none found" from "lookup silently skipped".
- Workaround: treated it as none.

## Question: the revision of each failing task

- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: the revision (and repo) next to each task ID, as `fx-tests task` prints it.
- Got: task IDs and days only; needed 11 separate `fx-tests task <id> --profiles` calls to find which revisions failed and where the last failure sat relative to a landing.
- Workaround: loop over `fx-tests task`.
