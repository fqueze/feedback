## Precise marker times, and zooming between two markers

- Command: `profiler-cli thread markers --category Test --search browser_opentabs_hover_preview --list --limit 0 --session S`
- Expected: timestamps precise enough to tell which markers came before the failure. All of them happened within about 100 ms of each other.
- Got: every row shows `t=3m30s` or `t=3m31s`, so the order within the second can't be read.
- Also: `profiler-cli zoom push m-29,m-16` failed with `Invalid time value: "m-29"`. It accepts `m-N` for one marker's own range, or `ts-X,ts-Y`, but not a pair of instant markers.
- Workaround: `marker info m-29 m-31 --json` to read `start`, then `zoom push 210.356,210.469`.
- What would have helped: millisecond precision in `--list` output, like `t=210.468s`, when the view spans minutes, and `zoom push m-A,m-B` meaning start of A to start (or end) of B.
