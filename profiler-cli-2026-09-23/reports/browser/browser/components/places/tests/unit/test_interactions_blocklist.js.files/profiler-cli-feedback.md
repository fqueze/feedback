## Question: "which tests started after time T, and with what status"
- Command: `profiler-cli zoom push 49.5,125` then `thread markers --search name:test --list --limit 0`
- Expected: only markers starting in the range (or an option such as `--starts-in`).
- Got: every marker overlapping the range, including long `parallel`/test markers that started earlier, so "did any test launched after the onset pass?" is not answered.
- Workaround: `--json` and a Python filter on `flatMarkers[].start`.
