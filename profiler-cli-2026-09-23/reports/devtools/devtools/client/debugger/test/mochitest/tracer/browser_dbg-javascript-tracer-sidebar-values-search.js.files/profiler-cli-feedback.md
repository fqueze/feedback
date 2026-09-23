## Which markers' stacks contain a given function (2026-09-22)

Question: "did `ToolboxController.js!setCanRender` run on the parent main thread between 14.461 s and 14.60 s?" Samples were too sparse to answer it (347 samples over 15 s; `thread functions --search setCanRender` found nothing), so the evidence had to come from the stacks attached to `SetNeedStyleFlush` markers.

- Command: `profiler-cli thread markers --session S --search "name:SetNeedStyleFlush" --list --limit 0 --json`, then a shell loop running `profiler-cli marker stack <handle>` on each of 115 handles and grepping for `setCanRender`. That is 115 CLI round trips per window, repeated for three windows.
- Expected: a way to filter markers by a frame in their stack, e.g. `thread markers --stack-search setCanRender` (or `--has-stack-frame`), listing the matching markers with their times.
- Got: `--search` matches only the name, category and payload fields, not stack frames.
- Workaround: the loop above.
