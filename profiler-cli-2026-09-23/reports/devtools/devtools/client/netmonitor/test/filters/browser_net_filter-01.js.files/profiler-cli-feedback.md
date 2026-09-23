## How many tests ran in this job, and with which status (review-browser_net_filter-01.js)

- Command: `profiler-cli thread markers --session <s> --category Tasks --list --limit 0` on a resource-usage profile
- Expected: a way to see the per-status count of `test` markers (123 PASS, 0 FAIL) to confirm a job's "0 failing" claim.
- Got: every Tasks marker listed one per line, `test` markers mixed with the manifest-level ones; no aggregate.
- Workaround: piped through `awk '$2=="test"' | awk '{print $6}' | sort | uniq -c`.
- What the output could show: a summary line per marker name and status field when listing, e.g. "test: 123 (PASS 123)".
