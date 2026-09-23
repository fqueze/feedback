# profiler-cli feedback — test_group_overscroll_handoff.html

## Log markers (mochitest `info()`) print "(empty)" although the payload has the text

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_group_overscroll_handoff.html-1` (zoomed on the test), and `profiler-cli marker info m-7500`
- Expected: `INFO  helper_position_fixed_scroll_handoff-2.html | Start: fixed=1800 window=0`
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`. `marker info m-7500 --json` shows `"value": "helper_position_fixed_scroll_handoff-2.html | Start: fixed=1800 window=0"` with `"formattedValue": "(empty)"` for `message`, and `"value": "INFO"`, `"formattedValue": "(empty)"` for `level`.
- Workaround: `--json` on every INFO marker I want to read. The test's own `info()` lines are most of what a mochitest-plain log says about its state, so this hides half of the test's log.

## Session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session ...`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the error message suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked. Cost one failed load. (The suggestion in the error was good.)

## Review: "in what order, to the millisecond, did these markers happen?" (review-test_group_overscroll_handoff.html)

- Command: `profiler-cli thread markers --session review-toh-1 --thread t-64 --search "SetTargetAPZC,ContentReceivedInputBlock,ReceivePanGesture,ReceiveScrollWheel" --list --limit 0`, zoomed on 173.9-176.8 s
- Question: which pan and wheel events got a SetTargetAPZC and a ContentReceivedInputBlock, and which did not. The answer depends on sub-millisecond ordering: a response lands 0.2-0.4 ms after its event.
- Got: every row printed `t=2m54s` / `t=2m56s`, so 100 rows over 2.9 s had three distinct timestamps. The list could not answer the question.
- Workaround: `--json`, then `jq '.flatMarkers[] | .start'` on every list I read. Once zoomed below a few seconds, `--list` could print times as `t=174.2079s`.
