## Question: which of these markers' stacks go through function X?

- Command: `profiler-cli thread markers --session <s> --search LSan --list`, then `profiler-cli marker stack m-N` for each of 28 `LSan Leak` markers, grepping for `CallFunctionWithAsyncStack`.
- Expected: a way to filter markers by a function in their stack (e.g. `--stack-search CallFunctionWithAsyncStack`), or `--list` to print the frames that matter.
- Got: `--search` matches name/payload only, `--has-stack` only says a stack exists; took 28 `marker stack` calls in a shell loop.
- Workaround: shell loop over handles with `grep -c`.
