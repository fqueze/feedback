## Question: exactly when did this test start, relative to the first launch failure?

- Command: `profiler-cli thread markers --search test_setBreakpoint-on-line.js --list --limit 0 --session <s>` (and `marker info m-1`)
- Expected: start times precise to the millisecond. The differences here are 37 ms (start to "will retry") and 1.78 s (break to start).
- Got: `t=2m13s` in the list, and `Time: 2m13s - 2m58s` from `marker info`. Both are rounded to the second once past 1 minute.
- Workaround: `--list --json` piped to a Python script that prints `start`.
- What would have answered it: a millisecond start time (for example `t=133.452s`) in `--list` and `marker info`, at least when list entries are less than a second apart.

## Question: how many TIMEOUT tests started after time X, and did any test pass after it?

- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json`, then a script that counts by status and splits at a timestamp.
- What would have answered it: a `--group-by field:status` summary over a `zoom push X,end`. I did not try that, so it may already work; if it does, the guide could list it.
