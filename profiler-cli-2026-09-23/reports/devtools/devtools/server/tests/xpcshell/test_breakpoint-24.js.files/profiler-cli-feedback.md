## Question: "the exact start time of a marker after the first minute"

- Command: `profiler-cli thread markers --search test_breakpoint-24 --list --session <s>` on LKys8j8iTQKTAN9hKv040g's resource-usage profile
- Expected: start times precise enough to order markers ms apart (test start vs its "will retry" line, 37 ms later).
- Got: `t=2m13s` for both; `marker info` text also prints `Time: 2m13s`.
- Workaround: `marker info m-1 m-3 --json` and read `.markers[].start`.
- Could have shown: milliseconds in the `m:ss.mmm` form past 60 s, as it does below 60 s (`t=46.420s`).
