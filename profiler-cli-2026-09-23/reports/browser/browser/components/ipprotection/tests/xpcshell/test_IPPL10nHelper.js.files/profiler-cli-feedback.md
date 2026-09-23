## Question: how many ms apart are these markers?

- Command: `profiler-cli thread markers --session <s> --search test_IPPL10nHelper --list --limit 0` and `profiler-cli marker info m-152 m-165 ...`
- Expected: start times precise enough to order and space events that are milliseconds apart (READY assertion, `exiting test`, the shutdown warnings, the assertion).
- Got: every row reads `t=4m26s` (and `Time: 4m26s (instant)` in `marker info`) on a 15-minute resource-usage profile, so ~150 markers in one second are indistinguishable in time.
- Workaround: `marker info ... --json` and a script printing `.start`.
- What the default output could show: sub-second precision (e.g. `t=265.870s` or `4m25.870s`) when listed markers share the same rounded time, or always in `marker info`.

