## Question: "from when did every test in this job fail, and what was running at that moment?"

- Profile: resource-usage profile of an xpcshell job (Q6pDc4X0SYOXqgi4KIpuzA).
- Command used: `profiler-cli thread markers --search name:test --list --limit 0 --json` piped to a Python script bucketing marker start times by status.
- What the default output could have shown: a status-over-time summary for `test` markers (e.g. per 5 s: PASS/FAIL/TIMEOUT/SKIP counts), which would show at once that all 475 TIMEOUTs start within 45-50 s. The `--group-by` options group by field but not by time bucket.
