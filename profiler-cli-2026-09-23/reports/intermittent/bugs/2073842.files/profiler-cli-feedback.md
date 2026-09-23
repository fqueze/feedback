## Resource-usage profile of a task killed at max-run-time does not load

- Command: `PROFILER_CLI_SESSION_OWNER=2073842 profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/SyIIMq4dQUie7JjuhuwPAw/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session 2073842-1`
- Expected: the job timeline (CPU Use markers, the `Phase` and `Running cycle ...` markers), since this is the only profile a task aborted by Taskcluster uploads.
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11668 (line 2 column 1)`. The file is the unfinished line-delimited stream (`{"type":"meta"...}` then one `{"type":"marker",...}` per line) that mozsystemmonitor writes before finalizing. `fx-tests task` recognizes this case and says so; profiler-cli gives a JSON parse error that does not say why.
- Workaround: downloaded the file and parsed the lines with a Python script to bucket `CPU Use` markers per minute.
- Question the default output could have answered: "was the machine busy or idle while the job hung until it was killed?" Loading the partial stream (or at least a message naming the format) would have.

## A failed load leaves its daemon running (review-2073842)

- Command: `PROFILER_CLI_SESSION_OWNER=review-2073842 profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/SyIIMq4dQUie7JjuhuwPAw/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session review-2073842-1`
- Expected: after `Error: Profile load failed: Unserializing the profile failed: SyntaxError ...`, no session left behind.
- Got: `profiler-cli session list` still showed `review-2073842-1` with a live daemon (pid 1550552, 160 MB RSS) a minute later. An agent that trusts the error and moves on leaks a daemon per failed load, which counts against the "at most two loaded" budget.
- Workaround: `profiler-cli stop review-2073842-1`.
