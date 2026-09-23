## Question: when exactly did these log markers happen (sub-second)?

- Command: `profiler-cli thread markers --session <s> --search "neterror,navigate_to_errors" --list --limit 0` on a 23-minute resource-usage profile.
- Expected: timestamps precise enough to order and cite events (e.g. `t=678.243s`).
- Got: `t=11m18s`, rounded to the second, so several consecutive markers share one timestamp.
- Workaround: `profiler-cli marker info m-48 m-50 ... --json` and read `start` from a script.
