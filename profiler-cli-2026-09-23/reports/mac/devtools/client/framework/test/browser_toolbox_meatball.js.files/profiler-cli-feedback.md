## `marker info --json` truncates stacks to 20 frames; `marker stack` does not

- Command: `profiler-cli marker info m-443 m-444 ... --json --session browser_toolbox_meatball.js-1` (SetNeedStyleFlush markers, AEraUR2dTQ6QYtGIw6K2fQ meatball profile)
- Expected: the same stack `marker stack m-443` prints, or a pointer to it.
- Got: `"truncated": true` and the stack cut after `react-dom.mjs!scheduleRootUpdate`, so the caller (`inspector.js!#renderSplitOrientationMenu`, frame 28 in `marker stack`) was missing. I first classified that marker as an unknown "root render" MenuButton and spent a Linux profile load before noticing `marker stack` shows the full 34 frames.
- Workaround: `profiler-cli marker stack <handle>` one handle at a time.

## Question: which markers have a stack going through function X?

- Question: "which SetNeedStyleFlush markers in this range were caused by the `HTMLTooltip` constructor, and from which caller" (to order the creation of XUL tooltip panels in one document).
- Command: `thread markers --search SetNeedStyleFlush --list --limit 0 --json`, then `marker info <all 246 handles> --json`, then a Python filter over `stack.frames[].nameWithLibrary`.
- What would have answered it: a stack filter on `thread markers` (e.g. `--stack-contains HTMLTooltip`), listing time plus the first few JS frames of each match.
