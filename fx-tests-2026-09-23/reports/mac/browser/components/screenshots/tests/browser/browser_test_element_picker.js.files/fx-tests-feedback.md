## Which revision each failure ran on

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <taskId>` 14 times
- Question: did each failing run include a given landing (bug 1970802, which landed, was reverted, then relanded)?
- Got: `--task-ids` lists task ids by date but not the revision; I needed one `fx-tests task` call per task (and a script over `--json` for the full hash).
- Could have shown: the tree and revision next to each task id.

## `--bugs` printed nothing, and missed the closed tracking bug

- Command: `fx-tests test browser/components/screenshots/tests/browser/browser_test_element_picker.js --bugs`
- Question: which bug tracks this test?
- Got: output identical to the run without `--bugs`, with no "no annotated bugs" line; `annotatedBugs` is `[]` in `--json`. Bugzilla has bug 1984687 "Intermittent .../browser_test_element_picker.js | single tracking bug" (RESOLVED INCOMPLETE 2026-08-31), found with a Bugzilla REST summary search.
- Could have shown: an explicit "no sheriff-annotated bug in the window" line, and bugs whose summary names the test even when closed or not starred recently.
