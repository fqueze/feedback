## Question: how many ms after the test started did the harness log "will retry", and how long after the break did the test start?

- Command: `profiler-cli thread markers --session <s> --search test_xpcshell_debugging --list --limit 0`
- Expected: timestamps precise enough to subtract (the gaps are 37-78 ms and ~2 s).
- Got: past one minute, times print as `t=1m32s` / `t=2m14s`, rounded to the second, so both markers of interest read `t=2m14s`. Below one minute they print as `t=46.992s`.
- Workaround: `--json` and a Python one-liner over `flatMarkers[].start`, once per profile, and `marker info --json` for single markers.
- What the default output could show: millisecond precision at every magnitude (`t=2m14.607s`), as it already does below one minute.
