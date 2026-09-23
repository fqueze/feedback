## "How much network traffic / CPU did the machine have during test X?" (resource-usage profile)

- Command: `profiler-cli thread markers --search name:NetIO --list --limit 0 --json` (and `name:CPU Use`, `name:test`), then a Python script summing NetIO `sent_bytes`/`recv_bytes` and averaging CPU over each `test` marker's interval, and listing intervals with a > 5 KB send.
- Expected: a way to aggregate interval markers of one type over another marker's range (e.g. `zoom push m-42` then `thread markers --search name:NetIO` giving summed Sent/Received fields), so "did the license request go out, and when, relative to the test start" is one command.
- Got: `thread markers` with a zoom gives only counts and duration stats per marker name, not sums of payload fields; had to script over `--json`.
- Also: in `--json`, the `CPU Use` marker's `cpuPercent` field value is a string (`"6.9%"`) rather than a number, unlike NetIO's byte fields.
