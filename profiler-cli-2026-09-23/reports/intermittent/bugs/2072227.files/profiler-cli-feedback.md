## Cannot load the streamed (JSONL) build resource profile a failed mach build leaves behind

- Command: `PROFILER_CLI_SESSION_OWNER=2072227 profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/d8EXGUHCRw25WAzgnZE3ow/runs/0/artifacts/public/build/profile_build_resources.json" --session 2072227-1`
- Expected: load the markers that were streamed (mozsystemmonitor `start_streaming` writes one `{"type":"meta"}` line, one `{"type":"thread"}` line, then one `{"type":"marker",...}` line per marker; the file is only replaced by a full profile when the build ends normally). Tolerating a truncated last line would also help: here the file was cut mid-line.
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11429 (line 2 column 1)`. The session was left registered with that error and had to be stopped by id.
- Workaround: parsed the JSONL with a Python one-off to count markers and find the last timestamp.
- Every failed or killed `build-*` task in CI leaves this format, so it is the resource profile one would want for build bustage.
