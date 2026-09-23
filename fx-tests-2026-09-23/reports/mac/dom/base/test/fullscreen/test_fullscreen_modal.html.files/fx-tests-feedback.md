## Question: "the task IDs and failure messages of every failing run of one test on a try push"

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --test dom/base/test/fullscreen/test_fullscreen_modal.html --all-jobs --messages --task-ids --profiles --full-messages`
- Expected: the per-config table plus, for this one test, the task IDs, the messages and the profile URLs (the flags were accepted silently).
- Got: only the per-config ran/passed/passed-on-retry/failed table; `--task-ids`, `--profiles` and `--messages` were ignored with no warning.
- Then `fx-tests try <rev> --task-ids --profiles --messages --full-messages --limit 0` (no `--test`): the test's row printed 5 task IDs and "… 3 more tasks", 6 profile URLs and "… 3 more profiles", despite `--limit 0`; and the messages were not printed for that row at all.
- Workaround: `--json`, then `newIntermittents[].taskIds` / `.allMessages` / `.profiles` for the path. That had the 8 tasks and the message counts (7x "Not in fullscreen mode", 1x "Fullscreen request aborted").
- What the output could have shown: with `--test`, the task IDs/messages/profiles for that test; and `--limit 0` lifting the per-row "… N more" truncation.

## Question: "the per-test profile URL of one failing run"

- Command: `fx-tests task b01qiLslTfCkZl6AjQM8Kg --profiles --full-messages` (and `fx-tests try 9db28f6db8bf... --profiles --json`, `newIntermittents[].profiles` for this task)
- Expected: `profile .../profile_test_fullscreen_modal.html.json` under dom/base/test/fullscreen/test_fullscreen_modal.html, as for the other 7 jobs.
- Got: no profile line for that test in that job (the other failing tests of the same job did get one), and no `testProfiles` in the try JSON for this task.
- The job's own resource-usage profile has the marker "FAIL profile uploaded in profile_test_fullscreen_modal.html.json", and the artifact exists (HTTP 200, 17 MB).
- Likely cause: in this run the test's first failure message was the teardown one ("TypeError: Fullscreen request aborted", after a timeout), and the "profile uploaded" line came after "called finish() multiple times" / "addAssertionCount should only be called by a cross origin test" errors; the tool may only look at a subset of messages.
- Workaround: built the URL by hand from the name in the resource-usage marker.
