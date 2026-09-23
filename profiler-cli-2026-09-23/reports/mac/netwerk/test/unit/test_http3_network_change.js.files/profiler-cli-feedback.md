## `load` dies silently on an 872 MB (uncompressed) xpcshell profile

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=test_http3_network_change.js profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/A5PNp98bT2m_eFo7DQeSzw/runs/0/artifacts/public/test_info/profile_test_http3_network_change.js.json --session test_http3_network_change.js-1`
- Expected: the profile loads, or an error saying why not (size, string length, heap).
- Got: first try, `Error: Session ... is not reachable. Nothing is accepting connections on ....sock. The daemon exited without cleaning up.` Second try, the `load` hung past 120 s and then the daemon was gone. The daemon log ends at `Fetching profile from ...` with no error line.
- The artifact is 48.6 MB gzipped and 872,396,782 bytes of JSON once decompressed, which is past V8's maximum string length (~512 MB). About 800 MB of it is 4.5 million `Task DummyEvent` markers plus the same number of `TaskController::AddTask` FlowMarkers, all on GeckoMain. Every per-test profile of this test in CI is this size (48–54 MB gzipped, 6 checked), and so are several other macOS xpcshell failure profiles in the same jobs (test_ext_sandbox_csp.js, test_search_suggestions.js, test_ext_contentscript_context_isolation.js, ...).
- Workaround: stream-parsed the gzipped JSON with a Python `raw_decode` loop, dropping DummyEvent/FlowMarker entries (`extract_markers.py` in this directory). Because nothing could be loaded into a session, `profile-link.py` could not build a single link, so the report has no profiler links.
- What would help: stream-parse the profile, or at least log the parse error and exit with it. An option to drop marker types while loading (`--drop-markers DummyEvent`) would make these profiles usable.

## `load` needs PROFILER_CLI_SESSION_DIR in the sandbox

- Command: `profiler-cli load <url> --session test_http3_network_change.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message itself suggests `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked. Minor, but the brief doesn't mention it and profile-link.py relies on the same variable.

## Question: "how long did this subtest take, from one log line to the next?"

- Command: `profiler-cli thread markers --search test_http3_network_change --list --limit 0 --session test_http3_network_change.js-r1` (resource-usage profile of A5PNp98bT2m_eFo7DQeSzw)
- The default list prints `t=11m45s` / `t=12m45s`, rounded to the second. That is enough to see a ~60 s gap, but not to put the 59.37 s subtest next to the 60 s budget, or to line it up with the per-test profile's millisecond timestamps.
- Workaround: `--json` plus a script printing `start/1000` to the millisecond.
- What the output could show: timestamps with millisecond precision in `--list`, at least when the view spans more than a minute. A `--since <handle>` delta column would be better still.

## Question: "what was the machine's average CPU over this test's time range?"

- Command: `profiler-cli zoom push 704.9,770.5` then `profiler-cli thread markers` on a resource-usage profile. `counter list` says `No counters in this profile`, and `profile info` says `No significant activity`.
- The CPU data exists only as `CPU Use` interval markers (one per ~100 ms, `cpuPercent` in the payload). Getting the mean, min and max over a range took `--json` plus a script.
- What the output could show: for resource-usage profiles, a CPU summary (mean, min, max `cpuPercent`) over the zoomed range, in `profile info` or `thread markers`' aggregate for `CPU Use`.

## Correction to the first entry

- "the report has no profiler links" applies to the per-test profiles only. The jobs' resource-usage profiles (about 1 MB) loaded fine, and the report links to them.
