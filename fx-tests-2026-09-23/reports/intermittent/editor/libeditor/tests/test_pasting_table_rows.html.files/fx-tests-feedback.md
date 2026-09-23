## "Issues (first failure per run)" names an expected todo() as the failure

- Command: `fx-tests test editor/libeditor/tests/test_pasting_table_rows.html`
- Expected: the first *unexpected* failure of each failing run, e.g. `Timed out while polling clipboard for initialized data` or `An input whose "inputType" is insertFromPaste should've been fired on div`.
- Got: `22x FAIL Rows to select (r1,r2,r3,r4) contains row in <tbody> and <thead> or <tfoot> of table "t3", see bug 1667786.` as the top issue. That message is a `todo(false, ...)` (status FAIL, expected FAIL) that every run of the test emits, passing or failing; the log prints it as TEST-PASS / known fail. So the headline failure mode is wrong for 22 of 25 runs, and the real ones only show up with `fx-tests task <id> --messages`.
- Workaround: read `fx-tests task <taskId> --messages` per job, and the raw log, to find the real first failure.
- Question it should have answered: "what is the first unexpected failure in each failing run of this test?" Filtering on expected != status (or dropping expected-FAIL) would.
