## Resource-usage profile of an aborted task does not load

- Command: `PROFILER_CLI_SESSION_OWNER=test_marionette.py.raise-for-port profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/dScCEuIlT_ysHm74ZNDing/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session test_marionette.py.raise-for-port-1`
- Expected: the job's timeline (test markers, CPU), which is exactly what a job killed at max-run-time needs.
- Got: `Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11518 (line 2 column 1)`. The task was aborted before the harness finalized the file. It is left in a streamed form, one JSON object per line (88k–180k lines, starting with `{"type":"meta",…}`), while a finished job's is a single JSON document. This was the case for every aborted task I checked (4 of 4).
- Workaround: none. I used the job logs only.
- Suggestion: accept the streamed JSON-lines form (or give an error naming it), since aborted jobs are the ones most in need of their timeline.

## A failed load leaves its session daemon running (review-test_marionette.py.raise-for-port)

- Command: `PROFILER_CLI_SESSION_OWNER=review-test_marionette.py.raise-for-port profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/O-apgXYUTXWCzRNNfy0iHg/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session review-test_marionette.py.raise-for-port-1`
- Expected: when `load` fails with `Profile load failed: Unserializing the profile failed`, no session remains.
- Got: `profiler-cli session list` still showed `review-test_marionette.py.raise-for-port-1` with a live daemon pid. An agent that trusts the error would leave it running and count it against the two-session limit.
- Workaround: `profiler-cli stop <id>` after a failed load.
