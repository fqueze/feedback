## Marker list times lose precision once a profile is longer than a minute

- Command: `profiler-cli thread markers --search "getGrids,getFragments,..." --list --limit 0 --session <s>` on a 1m50s per-test profile (task P7nUS5-uSaexlnrMgFNjQQ).
- Question: in which order did these RDP requests and target-destroyed events happen, within one second?
- Expected: start times with millisecond precision, as for a short profile (`t=4.518s`).
- Got: every row printed as `t=1m48s` or `t=1m49s`, so the order within that second, which was the question, cannot be read.
- Workaround: `--json` and a script printing `start/1000` with three decimals.
