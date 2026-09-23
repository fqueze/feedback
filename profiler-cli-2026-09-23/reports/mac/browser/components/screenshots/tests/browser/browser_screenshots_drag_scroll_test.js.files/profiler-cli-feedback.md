## Question: which markers in a range have a stack going through a given JS function?

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --json --session <s>` piped to a Python script to print handle and start time, then `marker info` on each candidate to read its stack.
- Wanted: of the ~25 `SetNeedStyleFlush` markers around a mousedown, the ones whose stack contains `updateWindowDimensions` (the `toggleAttribute("resizing")` calls), without opening each one.
- Got: `--search` matches the marker name and payload only, not the stack, so the list printed ~25 identical rows (`SetNeedStyleFlush  t=44.443s  instant  ✓`), and the only way to find the two that mattered was `marker info` one by one.
- What would have answered it: a stack filter on `thread markers`, e.g. `--stack-search updateWindowDimensions`, or the leaf JS frame of the stack shown in the `--list` row for markers that have one.
- Workaround: `--json` + script for sub-ms start times to narrow candidates, then `marker info` on each.
