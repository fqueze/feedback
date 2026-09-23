## Question: how long was the gap between two consecutive markers in a long profile?

- Command: `profiler-cli thread markers --session <s> --search test_default-browsers --list --limit 0`
- Expected: timestamps precise enough to see a 2.1 s gap between consecutive log lines.
- Got: `t=3m55s` / `t=3m57s`, rounded to the second in an 18-minute resource-usage profile. The
  gap that mattered (235.040 s to 237.176 s, the 2 s `ShutdownWithTimeout`) and the ms spacing of
  the shutdown warnings only showed up with `--json` and a Python script.
- Could have shown: ms precision on `--list` rows (e.g. `t=3m55.040s`), or a delta-from-previous
  column.
