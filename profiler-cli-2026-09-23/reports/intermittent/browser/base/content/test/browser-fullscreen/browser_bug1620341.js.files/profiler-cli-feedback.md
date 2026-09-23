## Question: "which markers were emitted from JS function X?" (e.g. every attribute write by `tabs.mjs!_updateCloseButtons`)

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --json --session <s>`, then a shell loop running `profiler-cli marker stack m-N` on each of ~280 handles, then grep on the stacks.
- Expected: a way to filter markers by a frame in their captured stack, e.g. `thread markers --search SetNeedStyleFlush --stack-contains _updateCloseButtons` (or show the top JS frame of the stack in `--list` rows).
- Got: `--search` only matches name/category/payload, so the stack was only reachable one marker at a time.
- Workaround: the loop (about 30 s per profile, 3 profiles). Output that would have answered it directly: `--list` rows with the first JS frame of the marker's stack.
## Question: "which line of this JS function made the DOM change?" (review of this report)

- Command: `profiler-cli marker stack m-1 --json --session review-browser_bug1620341.js-3` on the `SetNeedStyleFlush` from `tabs.mjs!_updateCloseButtons/</<` (marker index 76099).
- Expected: the line (and column) of each JS frame, as the profiler UI shows in stack tooltips when the frame table has them. Here it would tell `setAttribute` at tabs.mjs:1343 (the `overflowing` branch) from tabs.mjs:1359 (the narrow-tab branch).
- Got: function names only, in text and in `--json`.
- Workaround: none; left the branch undetermined (it does not change the conclusion).
