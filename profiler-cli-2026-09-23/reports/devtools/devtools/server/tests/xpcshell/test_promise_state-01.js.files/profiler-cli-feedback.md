## Test marker times over one minute are rounded to whole seconds in text output

- Command: `profiler-cli thread markers --session <s> --search test_promise_state-01 --list --limit 0` and `profiler-cli marker info m-86 m-88 m-91 --session <s>`
- Question: exactly when did the test's timeout fire, and how long after it started (ms precision, to line it up with the harness's 45 s kill timer)?
- Expected: `t=92.927s` or `t=1m32.927s`.
- Got: `t=1m33s` in both the list and `marker info` (`Time: 47.886s - 1m33s`), so start and end cannot be compared at ms precision past the 60 s mark.
- Workaround: `marker info ... --json` and read `start`/`end`.
