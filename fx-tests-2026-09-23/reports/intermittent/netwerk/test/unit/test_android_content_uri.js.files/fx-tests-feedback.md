# fx-tests feedback (test_android_content_uri.js)

## Question: "which passing jobs of this config ran this test?"

- Command: `fx-tests test netwerk/test/unit/test_android_content_uri.js --task-ids --limit 0` (and `--coverage --json`)
- Expected: a way to list a few task IDs where the test PASSED on a given config, to compare
  a passing job's artifacts (here the Android logcat) with a failing one's.
- Got: only failing task IDs; `--coverage --json` has per-config counts but no task IDs.
- Workaround: Treeherder `similar_jobs` API on a failing job id, then
  `fx-tests task <id> --json | grep test_android_content_uri` over ~20 candidates, because
  chunking varies per push and most similar jobs did not run the test. About 20 minutes.
- What would have answered it: `fx-tests test <path> --config <c> --passing-task-ids` (a
  handful of recent tasks where the test ran and passed).

## Question: "where is the device-side log of this failure?"

- Command: `fx-tests task eqnSNPu7TA6mee36pFQ4Ew.0 --profiles`
- Expected: for Android xpcshell jobs, where no per-test profile exists ("No failing test named
  a per-test profile in this job"), a pointer to the artifact that holds the runtime evidence.
- Got: only the resource-usage profile, which holds the harness-side log but nothing from the
  device's system_server.
- Workaround: listed the task artifacts by hand and found
  `public/test_info/logcat-emulator-5554.log`, which is where the whole diagnosis came from.
- Suggestion: print the logcat artifact URL in `task --profiles` output for Android jobs.

## `--task-ids --limit 0` prints the job list three times

- Command: `fx-tests test netwerk/test/unit/test_android_content_uri.js --task-ids --limit 0`
- Expected: one list, or one list per Issue with that Issue named in a heading.
- Got: "Task IDs (34 jobs)" followed by three date-grouped blocks (11, 22 and 34 jobs) with no
  heading saying what separates them. I guessed they were per Issue (TIMEOUT, FAIL, all), but
  could not tell for sure.
