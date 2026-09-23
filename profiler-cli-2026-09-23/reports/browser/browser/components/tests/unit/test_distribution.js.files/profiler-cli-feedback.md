## Question: "in which order, to the millisecond, did these log lines of concurrent tests happen?"

- Command: `profiler-cli thread markers --session <s> --search test_distribution --list --limit 0` on a 15m36s resource-usage profile, also after `zoom push 146.5,149.5`.
- Expected: start times with ms precision (the markers carry them; they are 1 ms apart), at least once zoomed into a 3 s range.
- Got: every row shows `t=2m27s` or `t=2m28s`, so ~70 markers spread over 150 ms cannot be ordered or compared with the per-test profile's times.
- Workaround: `--json` and print `flatMarkers[].start` / `duration` with a Python one-liner.
- Could have shown: the time with a precision matching the zoomed range (or always seconds with 3 decimals), and the end time for interval markers (`test` markers).

## Same question, hit again in review (browser-review-test_distribution.js)

- `thread markers --list` after `zoom push 1263.5,1266.5` and `286.0,288.5` on the other two resource-usage profiles: the same `t=21m4s`-style times. Worked around again with `--json` and a Python script, three times.
