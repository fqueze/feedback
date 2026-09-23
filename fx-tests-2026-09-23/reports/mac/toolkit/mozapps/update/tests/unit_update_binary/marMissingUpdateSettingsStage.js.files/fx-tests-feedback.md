## `--bugs` prints nothing when no bug names the test

Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marMissingUpdateSettingsStage.js --bugs`

Expected: a line such as "Bugs: none naming this test", or the list.

Got: the same output as without `--bugs`, exit 0, nothing on stderr. Silence reads the same as
"the flag was ignored" or "the query failed".

## Question: which error did each timeout of this test hit?

All 10 macOS failures are "Test timed out"; the discriminating line is an earlier
`JavaScript Error` (`NS_ERROR_FILE_ACCESS_DENIED ... [nsIFile.permissions]`) in the replayed log.
To check every run, I had to fetch each job's `live_backing.log` and grep. A per-run "first
error line of the replayed log" next to the timeout in `fx-tests test --task-ids` would have
split the timeouts into their real failure modes.
