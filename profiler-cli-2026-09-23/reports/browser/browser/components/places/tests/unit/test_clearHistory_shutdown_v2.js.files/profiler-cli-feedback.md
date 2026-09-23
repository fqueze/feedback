# profiler-cli feedback (test_clearHistory_shutdown_v2.js diagnosis)

## Question: "at what exact time, to the millisecond, did each of these markers happen?"

- Command: `profiler-cli thread markers --session <s> --search test_clearHistory_shutdown_v2 --list --limit 0`
- Expected: timestamps precise enough to order the markers and measure gaps. The question here was how long after `exiting test` the `Could not get the GreBinD directory` output arrived (it was 175 ms).
- Got: 30 markers over 0.5 s all printed as `t=2m54s`. Past one minute, the list rounds to whole seconds, even when zoomed to a 1-minute range.
- Workaround: `--json` and a python script printing `start`. A seconds-with-ms format (`t=174.006s`), or more precision when many rows share the same rounded value, would have answered it.
