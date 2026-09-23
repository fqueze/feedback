## Marker times past one minute lose their precision in `thread markers --list` and `marker info`

- Question: "how many ms after test A's end did the first launch failure come?" (two markers ~64 s into the profile).
- Command: `profiler-cli thread markers --session <s> --search "name:C++ warning" --list` and `profiler-cli marker info m-1 m-12 --session <s>`
- Expected: millisecond times, as the output gives below 60 s (`t=50.350s`).
- Got: `t=1m4s` / `Time: 47.432s - 1m4s`, so two markers 25 ms apart looked simultaneous.
- Workaround: `marker info ... --json` and reading `start`/`end`.
