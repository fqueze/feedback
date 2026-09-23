## Marker times past one minute lose their milliseconds (browser_markup_view-original-source.js)

- Command: `profiler-cli thread markers --session <s> --search "eventType:click,TimeoutExecutor,MessageEventToParent" --list --limit 0` on a 3m12s debug profile (task Q8y5VCO0Sd6UI5oLBGnuKQ); same in `marker info` ("Time: 1m43s").
- Expected: each marker's time to the millisecond, as below one minute (`t=38.397s`).
- Got: every row `t=1m43s`, so the order of ten markers within 100 ms could not be read.
- Workaround: `--json` and print `start/1000` with a script.
- Question it could not answer: "in what order, and how many ms apart, did these markers happen" in any profile longer than a minute, which is most per-test CI profiles.

## Which JS function dirtied style at each marker in a window (browser_markup_view-original-source.js)

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0` inside a 30 ms zoom.
- Expected: some hint of each marker's stack (its first JS frame) in the list.
- Got: bare rows (`SetNeedStyleFlush t=38.397s instant ✓`); to find the two markers that mattered among 101, I had to take the handles from `--json` and dump `marker info` for all of them to a file, then grep the stacks.
- Question: "which of these markers' stacks contain function X" (here `EventTooltipHelper.js!EventTooltip/<` vs `HTMLTooltip.js!show` under `TimeoutExecutor`). A top-JS-frame column, or a `--stack-search` filter, would answer it.

## Machine CPU at a moment, from the resource-usage profile (browser_markup_view-original-source.js)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list` zoomed on 800 ms of task CVvt5alEQSyMYnEet9r8IQ's resource-usage profile.
- Expected: the CPU percent on each row.
- Got: rows with only a duration (`CPU Use t=11m57s 101ms`), and times rounded to the second.
- Workaround: `--json` and read `fields[].cpuPercent` with a script.
- Question: "was the machine saturated at t", the first question of any timeout.
