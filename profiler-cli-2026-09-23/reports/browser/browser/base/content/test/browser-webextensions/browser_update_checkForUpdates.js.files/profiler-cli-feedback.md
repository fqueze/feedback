## Question: "when exactly did this window die, relative to the test's end?"

- Command: `profiler-cli thread markers --search about:addons --list --limit 0 --session <s>` on a 19-minute resource-usage profile.
- Expected: start and end times precise enough to order events a few hundred ms apart.
- Got: `t=3m24s  17.898s` — start rounded to the second past one minute, no end column. I had to run `marker info <m> --json` per marker and read `start`/`end`.
- Also: in `--list --json` under a `zoom push`, markers that end after the zoom range have `"end": null` (duration still set), which reads like "never ended".
- Would help: ms precision in the list (e.g. `t=204.283s`), and an end column for interval markers.
