## Question: in what order, to the millisecond, did the harness log lines in a 3 s window happen?

- Command: `profiler-cli thread markers --session <s> --search "name:DEBUG,name:INFO,name:test" --list --limit 0` after `zoom push 840,860` on a 22-minute resource-usage profile.
- Expected: start times precise enough to order lines that are 10-100 ms apart (e.g. `t=852.292s`).
- Got: `t=14m12s`-style times, rounded to the second, so dozens of lines in one second all read `t=14m`. Ordering a `ps` listing against a launch line was impossible from the text output.
- Workaround: `--json` and a script printing `start/1000` with 3 decimals. The option that would answer it: a `--precise-times` flag, or keeping ms precision in `--list` when zoomed into a range under a minute.
