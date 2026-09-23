## `--bugs` prints nothing when no bug names the test

Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marMissingUpdateSettings.js --bugs`

Expected: a line such as "Bugs: none naming this test" (or the list).

Got: the same output as without `--bugs`, exit 0, nothing on stderr either. Silence reads the same
as "the flag was ignored" or "the Treeherder/Bugzilla query failed". I had to query Bugzilla by
hand (`short_desc=marMissingUpdateSettings`) to confirm there is none.

## Question: which error did each failing run of this test hit?

All 13 failures are "Test timed out"; the real cause is an earlier `JavaScript Error` console line
in the replayed log (`NS_ERROR_FILE_ACCESS_DENIED ... [nsIFile.permissions]`). To check that all 13
runs failed the same way I downloaded all 13 `live_backing.log` and grepped them. A per-run "first
error/exception line in the test's replayed log" next to the timeout message in
`fx-tests test --task-ids` would have answered it directly, and would split timeouts into their
real failure modes.

