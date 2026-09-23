## The exact time of a marker, to compare two instant markers ms apart

- Command: `profiler-cli thread markers --session <s> --search backward_forward --list --limit 0` and `profiler-cli marker info m-595 m-596 m-597 --session <s>`
- Expected: a timestamp precise enough to tell how long after "Leaving test ..." the assertion fired (they are 15-17 ms apart).
- Got: `t=2m1s` in the list and `Time: 2m1s (instant)` in `marker info`; both round to the second, so every marker of the last second of the test reads the same.
- Workaround: `marker info ... --json` piped through a Python script reading `.start` (121041.831 vs 121058.831). The JSON's top level is sometimes a list, sometimes an object, depending on how many handles are passed, which also cost a retry.
