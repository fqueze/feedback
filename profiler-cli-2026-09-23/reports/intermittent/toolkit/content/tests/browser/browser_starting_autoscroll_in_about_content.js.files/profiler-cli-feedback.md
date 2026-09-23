## Question: exact times of a few markers, to measure gaps of a few seconds late in a 20-minute profile

- Command: `profiler-cli thread markers --session <s> --search ... --list --limit 0`
- Expected: a time column precise enough (ms) to subtract two markers 4 s apart.
- Got: `t=19m3s`, rounded to whole seconds once the profile is longer than a minute; `marker info` also prints `Time: 19m3s`.
- Workaround: `profiler-cli marker info m-N --json` per marker, reading `start`/`end` with a Python one-liner.
- What the output could have shown: the list's time column with ms precision (e.g. `19m3.445s`), or a `--precise-times` flag.
