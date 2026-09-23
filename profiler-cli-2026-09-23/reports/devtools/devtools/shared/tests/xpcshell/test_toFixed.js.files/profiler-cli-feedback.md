## Question: which tests were running or ending at time t?

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0`
- Expected: a way to list interval markers overlapping or ending within a window (e.g. `--overlapping 61.5,63.9`), with end times.
- Got: list rows show start and duration (rounded to "1m4s" once past 60 s, which loses sub-second precision); `zoom push` keeps markers that merely overlap, so long-running ones flood the list.
- Workaround: `--json` and a Python script computing `start + duration`.
