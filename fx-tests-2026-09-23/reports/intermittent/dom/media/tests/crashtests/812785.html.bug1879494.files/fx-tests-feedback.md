## Crashtests are invisible to `fx-tests test`

- Command: `fx-tests test dom/media/tests/crashtests/812785.html` (and `--history`)
- Expected: per-config rates, history and failing task ids for a crashtest, the way it works for mochitest/xpcshell.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". Only `fx-tests intermittent --bug` worked, and it only knows about sheriff-annotated jobs.
- Workaround: I queried the Treeherder jobs API per job type (`/api/project/autoland/jobs/?job_type_name=...`), then grepped each job's `crashtest_errorsummary.log` and `live_backing.log`. That meant about 200 log downloads to get the pass and fail counts.

## Question: "which manifests ran before this test in the failing jobs, and in the passing ones?"

- Commands: `fx-tests task <taskId>` and `fx-tests intermittent --bug 1879494`
- What I needed: for each job (passing or failing), the test's position (`N / M`) and the manifest that ran just before it (or the task's `MOZHARNESS_TEST_PATHS`). Here the whole diagnosis was "fails 100% of the time when dom/base/crashtests runs right before it", and that was only visible by scripting over logs and task definitions.
- What the output could show: a "preceded by" column (previous manifest) and the job's manifest list in `fx-tests task`. It could also group a test's passing and failing runs by manifest set, in `fx-tests test` or `intermittent --bug`.
