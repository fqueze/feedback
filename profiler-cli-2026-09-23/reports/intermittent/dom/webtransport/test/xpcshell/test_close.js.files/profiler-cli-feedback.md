## Question: how long did the test wait between two log lines?

- Command: `profiler-cli thread markers --session test_close.js-1 --search test_close.js --list --limit 0` (and `marker info m-87 m-58`) on a 10-minute resource-usage profile.
- Expected: marker times precise enough to measure the 30 s between `test test_webtransport_create pending` and `WebTransport connection rejected`.
- Got: `t=3m22s` / `Time: 3m22s`, 1 s resolution.
- Workaround: `marker info ... --json` and reading `.start` (202150.797 vs 232243.797). Printing milliseconds (`3m22.151s`) in `--list` and `marker info` would have answered it.

## Question: the test's log in a resource-usage profile, readable

- Command: `profiler-cli thread markers --session test_close.js-1 --search test_close.js --list --limit 0`
- Expected: one line per log message.
- Got: each Android `launch_application: am startservice ...` INFO marker printed in full, about 5 KB each, so most of the output was two of those lines.
- Workaround: `--json` piped through a script that truncates messages. Truncating long payloads in `--list` (full text stays in `marker info`) would avoid it.
