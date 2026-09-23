## Millisecond timestamps of instant markers (question: "how many ms after the test started did its thread end?")

- Command: `profiler-cli thread markers --session test_fetch-file.js-1 --search test_fetch-file.js --list --limit 0`, then `profiler-cli marker info m-1 m-3 m-6`
- Expected: start times precise enough to tell two markers 13 ms apart (test start at 65.866 s, "will retry" at 65.879 s).
- Got: `t=1m6s` for both in the list, and `Time: 1m6s (instant)` in `marker info`. Only `--json` (`start: 65865.915`) answered it.
- Workaround: `marker info ... --json` piped through python. The text output could print ms (e.g. `t=65.866s`) at least in `marker info`, or when the zoom is shorter than a few seconds.
