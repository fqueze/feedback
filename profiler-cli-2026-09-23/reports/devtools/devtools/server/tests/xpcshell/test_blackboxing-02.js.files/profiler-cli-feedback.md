## Question: at what exact time did this test start, and when did it log "will retry"? (ms precision past the first minute)

- Command: `profiler-cli thread markers --search test_blackboxing-02 --list --limit 0 --session <s>` on a 2-3 minute resource-usage profile (JOcVulCfRyynVd-aExDW1g, LKys8j8iTQKTAN9hKv040g).
- Expected: start times with ms precision, as shown for t < 60 s (`t=46.346s`).
- Got: past one minute, times are rounded to whole seconds (`t=1m31s`, `t=2m13s`, `t=2m58s`). So I could not tell the 36-39 ms gap between the test start and "will retry", or order the log lines of the replayed full log. `marker info` prints `Time: 2m5s` too.
- Workaround: `--list --json` and reading `flatMarkers[].start`, and `zoom push <a>,<b>` on a 1 ms window to see which lines fall between Begin/End of the full log.
- Could have shown: `t=1m31.382s` (or `t=91.382s`) in the list and in `marker info`.
