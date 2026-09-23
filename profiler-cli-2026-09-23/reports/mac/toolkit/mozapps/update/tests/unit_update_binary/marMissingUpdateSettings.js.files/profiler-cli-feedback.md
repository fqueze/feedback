## Daemon dies silently loading an 868 MB (uncompressed) profile

Command:
`PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=marMissingUpdateSettings.js profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/B61NIY-TSqyr8pPGZdTkOA/runs/0/artifacts/public/test_info/profile_marMissingUpdateSettings.js.json --session marMissingUpdateSettings.js-1`

Expected: the profile loads, or an error saying it is too large (the gzip is 48 MB, the JSON
868,634,847 bytes, above V8's ~512 MB maximum string length).

Got: `load` prints only "Loading profile from ..." and returns; the next command says
"Session ... is not reachable ... The daemon exited without cleaning up". The daemon log ends at
"Fetching profile from ..." with no error. Twice, same result. All 13 failing profiles of this
test are 868-870 MB uncompressed, so none of them can be read.

Workaround: see below in the report (reading the file outside profiler-cli).

## Question: exact start times of the tests overlapping a failure

Command: `profiler-cli thread markers --session marMissingUpdateSettings.js-ru1 --search name:test --list --limit 0`
(resource-usage profile, 25 min long).

Wanted: to line up, to the millisecond, when each of the ~60 update tests started relative to the
failing `nsIFile.permissions` error (they all start within 3 s). The list prints `t=6m23s` for all
of them, so I had to use `--json` and a script over `flatMarkers[].start`. Printing times with ms
precision (e.g. `t=6m23.054s`) when the list spans a zoom of a few seconds, or always, would have
answered it.

## (follow-up to the 868 MB entry) scope and workaround

Every xpcshell per-test profile I checked on test-macosx1500-aarch64/opt is that size:
the 13 `profile_marMissingUpdateSettings.js.json` (868-870 MB each, sizes read from the gzip
trailer), `profile_initialStateValidation.js.json` 881 MB and `profile_marSuccessComplete.js.json`
874 MB (sizes from the resource-usage profile's `artifact` markers). So no per-test profile of
this job is readable with profiler-cli today.

Workaround used: diagnosed from the job's resource-usage profile (which holds the test's log as
markers) plus the task logs; the per-test profile was not read at all.

