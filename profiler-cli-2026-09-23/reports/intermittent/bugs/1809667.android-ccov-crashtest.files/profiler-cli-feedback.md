## Partial resource-usage stream of a task killed at max run time cannot be loaded

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Qr7Jysh9Qeyf_PWui4KL-w/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session 1809667.android-ccov-crashtest-1`
- Expected: the job timeline (CPU Use, test, Phase markers) of the killed task, to link where the time went.
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11687 (line 2 column 1)`. The artifact is a JSONL stream (`{"type":"meta",...}` then one `{"type":"marker",...}` per line).
- Workaround: parsed the stream with a Python script (`cpu_stream.py`), so none of the ccov observations have profiler links.
- Question it could have answered: "was the machine saturated, and how far did the job get, when the task limit killed it" — the one question every max-run-time report asks.
