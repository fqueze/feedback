## `load` of a large profile: daemon dies silently, `load` exits 0

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=marPostUpdateEnvironment.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/FJATl84VR8iclfuypyQVbA/runs/0/artifacts/public/test_info/profile_marPostUpdateEnvironment.js.json" --session marPostUpdateEnvironment.js-1`
- The artifact is 62 MB gzipped, 875 MB of JSON once decoded (`curl -sL -o /dev/null -w '%{size_download}'`).
- Expected: the profile loads, or an error saying it is too large (likely over V8's ~512 MB max string length if the body is read into one string before `JSON.parse`).
- Got: first try, the daemon disappeared ~7 s after "Fetching profile from ..." and `load` printed "Session ... is not reachable. The daemon exited without cleaning up." Second try, `load` hung for over 5 minutes, then exited 0 with no "Profile loaded" line; the daemon was gone again. The daemon log ends at "Fetching profile from ...": no error, no reason. The stale `.sock` and `.json` stay behind.
- Workaround: check `x-goog-stored-content-length` with `curl -sI` on the artifact before loading, and pick another failing task whose profile is smaller.

## Question: "what was the whole machine's CPU, second by second, over this range" in a resource-usage profile

- Command: `profiler-cli profile info --session marPostUpdateEnvironment.js-ru1` and `profiler-cli counter list --session marPostUpdateEnvironment.js-ru1` on `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/OH-SrnKTQR2OByrOQSgJpw/runs/0/artifacts/public/test_info/profile_resource-usage.json`
- Expected: the machine CPU track, which in these profiles is carried by `CPU Use` markers (10 per second, `cpuPercent` field).
- Got: `profile info` said "CPU activity over time: No significant activity." and `counter list` said "No counters in this profile.", although the profile holds 19,324 `CPU Use` markers. `thread markers --search "name:CPU Use" --list` prints only name, time and duration, not the percentage.
- Workaround: `zoom push 684,722`, then `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python script averaging `data.cpuPercent` per second. What would have answered it: `profile info` (or `counter list`) summarising the `CPU Use` markers as the CPU track, with the same per-bucket "over time" view `counter info` gives.
