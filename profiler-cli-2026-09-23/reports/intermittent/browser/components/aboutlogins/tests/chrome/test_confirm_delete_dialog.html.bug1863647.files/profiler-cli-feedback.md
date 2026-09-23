## Question: "Which tests ran before this one in a job that hit max-run-time, and where did focus first fail?"

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/LED-4GBVS0KmSixMOZZA3Q/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session <name>`
- Expected: the job timeline (test markers, INFO lines), even if partial, since the resource-usage profile of a killed job is a newline-delimited stream (`{"type":"meta"...}` then one `{"type":"marker"...}` per line).
- Got: `Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11527 (line 2 column 1)`. The failed load also left a running daemon session behind (it showed in `session list` and needed `stop`).
- Workaround: downloaded the stream and parsed it with a Python script to list `test` markers and the `Unable to restore focus` INFO lines with timestamps.
- Could have shown: loading the streamed format (or at least its markers), so `thread markers --search "restore focus" --list` answers it.
