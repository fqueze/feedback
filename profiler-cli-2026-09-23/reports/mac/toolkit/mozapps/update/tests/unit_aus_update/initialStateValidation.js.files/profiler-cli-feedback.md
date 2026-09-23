## Daemon dies silently on a large profile

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=initialStateValidation.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/COszRY6cQ3eYMfxkW91UAg/runs/0/artifacts/public/test_info/profile_initialStateValidation.js.json" --session initialStateValidation.js-1`
- Expected: the profile loads, or an error saying why not (size, memory).
- Got: `Error: Session initialStateValidation.js-1 is not reachable. ... The daemon exited without cleaning up.` The first time straight away, the second time after 84 s of CPU. The daemon log ends at `Fetching profile from ...` with no error. The profile is 60 MB gzipped, 883,273,936 bytes uncompressed (macOS 15 xpcshell, likely the `DummyEvent` runnable churn), more than V8's maximum string length, which may be the cause.
- Workaround: none found for this profile; read another task's.
- Also: the default session dir `~/.profiler-cli` is not writable in this sandbox (`EPERM: operation not permitted, mkdir`), and the brief does not mention `PROFILER_CLI_SESSION_DIR`; the error message did name the fix.

## Machine CPU over a time range

- Question: what was the machine's CPU use while one test ran (resource-usage profile)?
- Command: `profiler-cli thread markers --session initialStateValidation.js-ru1 --search "CPU Use" --list --limit 0` (zoomed to the test's `test` marker)
- Expected: the CPU percentage per marker, or a mean/min/max summary over the view.
- Got: 363 rows with name, time and duration only; the value is in `marker info` or `--json` (`data.cpuPercent`). Needed a script over `--json` to average it per 5 s.
- Workaround: `--json` and Python.
