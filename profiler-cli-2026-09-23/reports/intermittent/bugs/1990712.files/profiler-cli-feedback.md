# profiler-cli feedback (1990712)

## Partial resource-usage streams cannot be loaded
- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/FijKq8dzSKy6l-KjfvuZTQ/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session 1990712-1`
- Expected: the job's timeline. For a job killed by the worker (max-run-time or memory guard), this JSONL stream (a `meta` line, a `thread` line, then one `marker` object per line) is the only record of the machine.
- Got: `Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11668 (line 2 column 1)`.
- Workaround: parsed the JSONL myself (python), so no profiler links for these jobs.

## Question: average of a marker payload field over a range
- Question: "what was the mean user% and system% CPU over this zoomed range" (to tell a core spinning in the kernel from one running user code).
- Command: `profiler-cli zoom push 330,450` then `profiler-cli thread markers --search "name:CPU Use"`.
- Got: counts and interval durations only. Needed `--list --limit 0 --json` and a script to average `user_pct` / `system_pct`.
- Could have shown: per-field min/mean/max for numeric (or `%`-string) payload fields in the aggregate view.
