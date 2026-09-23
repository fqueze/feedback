## Question: what is this test's failure on each config, verbatim?
- Command: `fx-tests test dom/base/test/chrome/test_nsITextInputProcessor.xhtml`
- Expected: the Issues list to show the failures (`TEST-UNEXPECTED-*`) of each failing run.
- Got: `3. 2x FAIL runCommitCompositionTests(): doCommit() should commit the composition with the last data - got "", expected "foo"`. That message is a `todo_is`, logged as `TEST-KNOWN-FAIL` (Status FAIL, Expected FAIL) in the per-test profile of UhEb3ZfOR-27_Z8GivMY2A. The real failure of those 2 runs, `20 is more than expected 0 assertions`, is not in the list at all; `fx-tests task UhEb3ZfOR-27_Z8GivMY2A --messages --limit 0` lists it next to the todo.
- Also: `1. 4x FAIL Failure details not recorded`, for 4 runs whose message `fx-tests task DeXCDdtDSyu6c_7sEZHCrA --messages` shows as `[SimpleTest.finish()] No checks actually run...` (6x).
- Workaround: `fx-tests task <id> --messages --limit 0`, then the per-test profile's TestStatus markers.

## Question: this test's entry in one job with many failures
- Command: `fx-tests task UhEb3ZfOR-27_Z8GivMY2A --messages --profiles | rg TextInputProcessor`
- Expected: the test's entry, or a way to ask for one test (`--test <path>`).
- Got: nothing; the list is truncated before it (76 failing tests) and the truncation line was cut off by my filter.
- Workaround: `--limit 0`. A `--test <path>` filter on `task` would answer it directly.

## Question: the per-test profiles of one failing test in one job
- Command: `fx-tests task DeXCDdtDSyu6c_7sEZHCrA --profiles`
- Expected: all six per-test profiles the job uploaded.
- Got: five (`.xhtml`, `-2`, `-4`, `-5`, `-6`). `profile_test_nsITextInputProcessor-3.xhtml.json` is missing, although the job's resource-usage profile has `profile uploaded in profile_test_nsITextInputProcessor-3.xhtml.json` at t=10m34s and the artifact answers HTTP 200. It is the profile holding the `this test already called finish!` moment.
- Workaround: build the URL from the `profile uploaded in` marker.
