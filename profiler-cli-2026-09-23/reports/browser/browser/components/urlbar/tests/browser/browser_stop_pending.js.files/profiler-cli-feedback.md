
## Review: marker times in `--list` output are rounded to whole seconds

- Question: at what time, to the millisecond, did each of these markers happen (a race between a
  timer and a refresh-driver tick 7 ms apart)?
- Command: `profiler-cli thread markers --search 'RefreshDriverTick,setTimeout,TEST-' --list --limit 0 --session <s>`
  on a 3m36s profile, zoomed to 400 ms.
- Expected: `t=213.919s`-style start times. Got: `t=3m34s` on every row, so the rows cannot be
  ordered against each other beyond list order, and durations alone do not place them.
- Workaround: `--json` and a Python one-liner printing `start`. The output could show start times
  with millisecond precision whenever the view range is under a few seconds.
- Also: `profiler-cli --session <s> zoom push …` fails with `unknown option '--session'`; it must
  follow the subcommand.
