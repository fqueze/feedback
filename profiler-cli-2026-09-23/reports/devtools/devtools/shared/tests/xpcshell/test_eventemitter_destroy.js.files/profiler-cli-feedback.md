## Question: "what was logged in these 10 ms?"
- Command: `profiler-cli zoom push 110.840,110.880 --session test_eventemitter_destroy.js-1` then `thread markers --list --limit 0 --search "-name:CPU Use,-name:Memory,-name:IO,-name:NetIO,-name:Sampling Interval"` (resource-usage profile)
- Expected: the instant log markers inside the 40 ms window, a few dozen rows.
- Got: 636 rows (87 KB), mostly long `test` and `Phase` interval markers that merely overlap the window, listed first.
- Workaround: `--json` and a Python filter on `start` within the range.
- What would have answered it: an option to list only markers starting inside the zoom (or instant-only).

## Question: "when exactly, to the ms, did this test start and log 'will retry'?"
- Command: `profiler-cli thread markers --search test_eventemitter_destroy --list --limit 0`
- Got: `t=1m6s` for both, 12 ms apart; `marker info` also prints `Time: 1m6s`. The 12 ms gap is the key evidence (launch failed at once).
- Workaround: `--json` and read `start`.

## Question: "was the machine idle while the test timed out?" (same as already logged by other reports)
- `thread markers --search "name:CPU Use" --list` rows carry no percentage; needed `--json` and `fields[0].value`.
