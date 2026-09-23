## Expected `todo` failures reported as the test's failure

- Command: `fx-tests test widget/tests/test_clipboard.html` (Issues section), and `fx-tests task <taskId> --messages`
- Expected: the `TEST-UNEXPECTED-FAIL` message, `Own web custom format should be exposed when pref is false`, as the "first failure per run".
- Got: `15x FAIL Should get empty string on clipboard type 1 - got null, expected ""` as the only failure issue. That is a `todo_is` which the log prints as `TEST-FAIL` (expected, KNOWN-FAIL) in every Android run. `task --messages` lists it and a second `todo_is` next to the real failure, without marking them as expected. The resource-usage profile's `TestStatus` markers have the same problem: they show `FAIL` with no `expected` field, so only the stack on the real failure tells them apart.
- Cost: the headline failure mode is wrong for every run of this test. I had to download the live log to find the real failure and to see that the test has a second failure mode (`The data for web … saved in clipboard should be matched - got null`, 3 runs).
- Workaround: grep `live_backing.log` for `TEST-UNEXPECTED-FAIL`.
