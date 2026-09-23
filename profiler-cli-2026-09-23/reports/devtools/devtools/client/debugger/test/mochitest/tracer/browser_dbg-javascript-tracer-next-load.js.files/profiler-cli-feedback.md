## Question: "did any marker in this range have a stack going through function X?"

- Command: `profiler-cli thread markers --session S --search SetNeedStyleFlush --list --limit 0 --json | jq -r '.flatMarkers[].handle'`, then a shell loop running `profiler-cli marker stack <h>` on each of the 326 handles and grepping for `setCanRender`.
- Expected: a way to filter markers by a function in their captured stack, e.g. `thread markers --search SetNeedStyleFlush --stack-includes setCanRender` (or a `stack:` search field), answering "was the toolbar ever rendered before the failure" in one call.
- Got: `--search` matches name/category/payload only; stacks are only reachable one marker at a time.
- Workaround: the loop above (326 daemon round trips, about a minute). The same question came up twice in this investigation (once for a passing window, once for the failing one).
