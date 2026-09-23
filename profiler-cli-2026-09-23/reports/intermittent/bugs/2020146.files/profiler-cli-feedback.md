# profiler-cli feedback (bug 2020146)

## Partial resource-usage stream of an aborted task cannot be loaded

- Command: `PROFILER_CLI_SESSION_OWNER=2020146 profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/PZK78DDCRV2uD4UnRMKYzg/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session 2020146-1`
- Expected: the profile loaded. The task was killed by generic-worker's memory guard, so the resource monitor only left its streamed format (a `meta` line, then one `marker` object per line).
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11668 (line 2 column 1)`.
- Workaround: parsed the lines with Python (`mem.py`). No failing run of this bug could be linked; the links point at finished runs of the same job instead. Same issue as logged for bug 1809667.

## Question: how does system memory evolve over a run?

- Command: `profiler-cli thread markers --session 2020146-2 --search name:Memory --list --limit 3`
- The list rows show only `Memory  t=2ms  102ms`, not the `used` value, so answering "did Memory Used grow linearly from 1.2 to 13.8 GiB?" needs one `marker info` per sample (5761 markers) or a script over the raw file.
- What would have answered it: the Memory counter as a `counter` (sparkline + over-time buckets), or the payload's main field in the list row.
