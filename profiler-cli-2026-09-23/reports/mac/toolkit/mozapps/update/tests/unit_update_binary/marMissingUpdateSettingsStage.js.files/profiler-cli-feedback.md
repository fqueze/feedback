## Daemon dies silently loading an 869 MB per-test xpcshell profile

Command:
`PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=marMissingUpdateSettingsStage.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/QnVSkExCTcOxj7lwSoUqgw/runs/0/artifacts/public/test_info/profile_marMissingUpdateSettingsStage.js.json" --session mmuss-1`
(52 MB gzip; the resource-usage profile's `artifact` marker says 869MB uncompressed)

Expected: the profile loads, or an error saying it is too large to load.

Got: `load` prints only "Loading profile from ..." and returns 0; the next command says
"Session ... is not reachable ... The daemon exited without cleaning up". The daemon log ends at
"Fetching profile from ..." with no error. Same result twice (sessions
`marMissingUpdateSettingsStage.js-1` and `mmuss-1`).

Workaround: diagnosed from the job's resource-usage profile, which holds the replayed test log as
markers; the per-test profile was not read.

## Question: at what millisecond did each of these log lines happen?

Command: `profiler-cli thread markers --session mmuss-ru1 --search marMissingUpdateSettingsStage --list --limit 0`

Wanted: the times of the test's log lines, to order the chmod denial against the other tests'
start times and the neighbouring failures (all within ~1 s). The list prints `t=3m59s` for every
row, so I had to use `--json` and a script over `flatMarkers[].start`. Printing ms precision
(e.g. `t=3m59.277s`) when rows share the same rounded second would have answered it.
