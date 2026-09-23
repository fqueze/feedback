# fx-tests feedback (bug 2045964)

## Question: which tests and crash triggers are behind a crash-signature bug's annotations?
- Command: `fx-tests intermittent --bug 2045964 --since 120 --limit 0`
- Expected: for a crash bug, the test that crashed in each annotated job, and its crash signature / reason.
- Got: "Tests named, per annotated job" is dominated by `ShutdownLeaks` (129x) and `leakcheck` (17x), which are fallout of the crash, not the crashing test. The crashing test is only in the errorsummary `crash` action or in PROCESS-CRASH lines.
- Workaround: listed each of the 260 tasks' artifacts, downloaded every `test_info/<uuid>.json`/`.extra` (230 MB), and classified them with a script (MozCrashReason, trigger frame above MozWalkTheStack, OOMAllocationSize, URL), then read errorsummary `crash` records for the test path. That took about 15 minutes of fetching and scripting.
- What would answer it: for each occurrence, the PROCESS-CRASH test path and signature, plus the `.extra` MozCrashReason, aggregated.

## Question: give me a passing run of this test on config X, to compare with the failure
- Command: `fx-tests test <path> --coverage` (shows msix passed 138/138), but there is no way to get a passing task ID.
- Workaround: Treeherder `/api/project/mozilla-central/jobs/?job_type_name=…msix-N&last_modified__gt=…` for each of 40 chunks, then grep each task's `manifests.list` for the test's directory.
- What would answer it: `--task-ids --passing`, or `--coverage --task-ids`, with one recent passing task per config.

## Question: WPT tests
- Command: `fx-tests test testing/web-platform/tests/fetch/api/crashtests/huge-fetch.any.js`
- Got: "No test path in the xpcshell and mochitest 21-day data contains …". Half of this bug's annotations are WPT jobs, so none of the rate/history tooling applied to them.
