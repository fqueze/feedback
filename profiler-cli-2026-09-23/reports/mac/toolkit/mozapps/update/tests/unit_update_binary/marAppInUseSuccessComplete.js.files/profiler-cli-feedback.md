## `load` of an 875 MB (uncompressed) per-test profile exits 0 but the daemon dies silently

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=marAppInUseSuccessComplete.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/PV_vRwmaTKS2UCTJWhZlaA/runs/0/artifacts/public/test_info/profile_marAppInUseSuccessComplete.js.json" --session marAppInUseSuccessComplete.js-1`
- Expected: the profile loads, or an error saying it is too large.
- Got: after ~70 s, `load` prints only "Loading profile from ..." and exits 0. The next command says "The daemon exited without cleaning up". The daemon log ends at "Fetching profile from ..." with no error. Three attempts, same result.
- Cause (inferred): the artifact is 55 MB gzipped but 875,023,954 bytes of JSON once decoded (checked with curl), above V8's maximum string length (~512 MB). Almost all of it is GeckoMain markers: a `DummyEvent` Task marker plus a `FlowMarker` every ~3 ms for the whole run (macOS `nsBaseAppShell::DispatchDummyEvent`). Every per-test profile of this test from the failing macOS 15 xpcshell jobs is the same size (51–60 MB gzipped).
- Workaround: none within profiler-cli. I read the resource-usage profile, and grepped the raw JSON for the test's own log markers.
- Would help: parse the stream instead of one string, and above all report the failure (non-zero exit, error in the log) instead of exiting 0.
