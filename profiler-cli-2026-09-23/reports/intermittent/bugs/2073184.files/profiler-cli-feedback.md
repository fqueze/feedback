## Partial (streamed JSONL) resource-usage profiles of killed tasks cannot be loaded

- Command: `PROFILER_CLI_SESSION_OWNER=2073184 profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Muk0F7ZlTYGMre7jnZK0vQ/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session 2073184-1`
- Expected: the profile loads (it is the only timeline a task killed for exceeding maxRunTime leaves behind).
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11693 (line 2 column 1)`. The artifact is a JSON-lines stream (`{"type":"meta",...}`, `{"type":"thread",...}`, then one `{"type":"marker",...}` per line) because the task was killed before the harness wrote the final document.
- Workaround: parsed the JSONL with a Python script (CPU Use / IO / test markers). No links possible for these jobs, so every job that actually timed out on this bug (61 of them) is unlinkable.

## Question with no command: "which IO marker in this range wrote the most?" (review-2073184)

- Command: `profiler-cli thread markers --session review-2073184-1 --search name:IO --list --limit 0` in a 89 s zoom.
- Expected: a way to find the `IO` marker the report quoted ("Written: 15.5MB"): payload values in the `--list` rows, or a sort or filter by a numeric field (e.g. top N by `write_bytes`).
- Got: 1787 rows showing only name, time and duration. `IO`/`CPU Use` markers have no tableLabel, so the rows show no payload.
- Workaround: `marker info m-13..m-25 --json` and scanning `fields` by hand. It worked here only because the marker was near the range start.
