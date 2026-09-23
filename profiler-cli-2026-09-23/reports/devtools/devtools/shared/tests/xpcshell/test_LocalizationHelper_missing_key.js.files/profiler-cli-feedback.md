## Question: "how long after the test started did it report 'will retry'?" (test_LocalizationHelper_missing_key.js)

- Command: `profiler-cli thread markers --session <s> --search test_LocalizationHelper_missing_key --list --limit 0`
- Expected: millisecond timestamps. The gap between the `test` marker start and the "will retry" INFO (11 ms) is the whole diagnosis.
- Got: `t=1m6s` for both. Past one minute the text output rounds to whole seconds, and `marker info` does the same (`Time: 1m6s - 1m51s (45.009s)`).
- Workaround: `--json` and read `start`. The text output could keep ms, as in `t=65.877s` or `1m5.877s`.

## Question: "which tests were running at time T, in each of several jobs?"

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then a script to keep the Test markers whose [start, start+duration] contains T, and to count PASS/TIMEOUT before and after T.
- Expected: something like `thread markers --overlapping <t>`, or `zoom push t,t` also keeping interval markers that span the point. That would show what was in flight when the worker broke, which is the leaker question.
- Got: `zoom push` over a range lists the markers in it, but I could not tell which interval markers only overlap it, and it has no status aggregation.
- Workaround: the script above, over 4 profiles.
