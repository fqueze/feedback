## How many ms apart are consecutive test-log markers of one test?

- Command: `profiler-cli thread markers --session <s> --search insertCSS --list --limit 0` on a 20-minute resource-usage profile (task H5HjU64oRhe8D3U8087t2w).
- Expected: start times precise enough to tell the gaps between `Entering testExecuteScript`, `Extension loaded`, `Run test case`, and the `FAIL` (tens of ms apart).
- Got: every row shows `t=11m18s`; `marker info` also prints `Time: 11m18s (instant)`. The ms were only in `--json` (`flatMarkers[].start`), which needed a script.
- What would have answered it: scale the displayed precision to the gaps within the listed rows (or to the zoom), e.g. `t=677.858s`, at least when the rows of a filtered list are less than a second apart.
