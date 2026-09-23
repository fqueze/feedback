## `thread markers --list` rounds timestamps to whole seconds past 1 minute

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_grids_grid-outline-highlight-area.js --list --limit 0`
- Expected: timestamps precise enough to order the test's log lines (they are ms apart), e.g. `t=1m31.406s`.
- Got: every line from "Toolbox opened" to the failure printed as `t=1m31s`; `marker info` also printed `Time: 1m31s`. The question "what happened between the rects being found and the mouseover (49 ms apart)" could not be answered from the default output.
- Workaround: `--json` and reading `flatMarkers[].start`.

## (review) `marker info <several handles> --json` drops the handles

- Question: what is the `markerIndex` of each marker a report links to?
- Command: `profiler-cli marker info --session <s> m-16 m-43 m-24 … --json`
- Expected: each record carrying the `m-N` handle it was asked for, next to its `markerIndex`.
- Got: records with `markerIndex`, name and start, but no `handle` field, so I had to match them back to the handles by order.
- Workaround: rely on the output order matching the argument order.

## (review) `thread samples-top-down` on a range with no samples prints an empty tree

- Command: `profiler-cli zoom push m-10 --session <s>; profiler-cli thread samples-top-down --session <s>`
- Expected: a line saying the range has 0 samples.
- Got: the `Top-Down Call Tree:` header and nothing under it. With `--search`, the message was "No samples matched", which also looks like a filter problem.
- Workaround: none needed; the empty tree meant no samples.
