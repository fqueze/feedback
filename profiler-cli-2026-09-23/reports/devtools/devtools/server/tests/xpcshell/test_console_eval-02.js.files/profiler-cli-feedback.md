## test_console_eval-02.js (2026-09-22)

### Question: which of these markers came first, within the same second?
- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0`
- Expected: start times precise enough to order events past the first minute (onset of launch failures vs. the end of another test, 25 ms apart).
- Got: `t=2m13s`, `t=1m4s`: once past 60 s, times are shown to the second only.
- Workaround: `--json` and a script reading `start` in ms.
- What could have shown it: keep millisecond resolution in `--list` output (e.g. `t=63.896s` or `1m3.896s`).
