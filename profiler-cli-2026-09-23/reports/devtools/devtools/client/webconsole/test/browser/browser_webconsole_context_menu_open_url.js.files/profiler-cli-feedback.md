## Precise marker times past one minute
- Question: at what time (to the ms) did "Opening the toolbox", the stuck Renderer "Composite #1", and the last parent RefreshDriverTick happen?
- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0 --session S`
- Got: `t=3m59s` for every row after the first minute; the Renderer composite start (237.437 s) and the parent's last tick (237.898 s) both print as `3m57s`, so their order is invisible.
- Workaround: `--json` and read `flatMarkers[].start`. Text output could keep ms precision (`t=3m57.437s`).
