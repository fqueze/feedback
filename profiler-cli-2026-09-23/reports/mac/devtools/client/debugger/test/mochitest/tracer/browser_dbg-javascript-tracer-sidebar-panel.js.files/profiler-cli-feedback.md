## Question: which markers in this range have function X in their stack?

- Context: to show that `ToolboxController.js!setCanRender` never ran during one toolbox open (and did run in a passing one), I needed every marker whose captured stack contains that function.
- Command I had to use: `profiler-cli thread markers --has-stack --list --limit 0` inside a `zoom push`, then one `profiler-cli marker stack m-N` per row (about 1,000 markers per 100 ms window, three windows), grepping the output.
- Expected: something like `thread markers --stack-search setCanRender` (or `--search stack:setCanRender`) listing the matching markers, or `thread functions`-style counts over marker stacks.
- Got: `--search` matches only name/category/payload; samples are too sparse in CI profiles (2 samples in 120 ms here) for `thread samples --search` to answer it.
- Workaround cost: several minutes of wall time and a shell loop per window; the absence claim ("no stack in this window has setCanRender") is only as good as the loop.
