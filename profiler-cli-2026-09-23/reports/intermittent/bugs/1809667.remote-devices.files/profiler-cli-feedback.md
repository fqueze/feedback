# profiler-cli feedback (1809667.remote-devices)

## Partial resource-usage stream still cannot be loaded (same as 1809667.files/profiler-cli-feedback.md)

- Command: `PROFILER_CLI_SESSION_OWNER=1809667.remote-devices profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/RrJwN9yORpO-0xF2OCJddQ/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session 1809667.remote-devices-1`
- Expected: the job's timeline loaded (the Bitbar pixel6 browsertime task was killed for max run time, so the file is the streamed format: a `meta` line, then one marker per line).
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11382 (line 2 column 1)`, and the failed load again left the session `1809667.remote-devices-1` running (`session list` showed its daemon), so it had to be stopped by hand.
- Workaround: parsed the lines with Python. This makes every max-run-time job unlinkable: no observation of this report could get a profiler link.
