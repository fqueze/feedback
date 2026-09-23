## Question: "which style changes did function X make during this window" (markers filtered by a frame in their stack)

- Command: `profiler-cli thread markers --session <s> --thread t-0 --search SetNeedStyleFlush --list --limit 0` inside a `zoom push`, then a shell loop running `profiler-cli marker stack m-N` on each of 179 markers and grepping for `syncRowHeight` / `set CSSStyleProperties.height`.
- Expected: a way to keep only markers whose stack contains a function (e.g. `--stack-search syncRowHeight`, or `--includes-function f-N` applied to marker stacks), listed with their times.
- Got: `--search` only matches name/category/payload, so the answer needed ~180 `marker stack` + `marker info --json` calls.
- What it could have shown: the 4 matching markers with their times and leaf frames, in one command.

## `zoom push` does not take a marker-to-marker range

- Command: `profiler-cli zoom push m-24,m-16 --session <s>`
- Expected: zoom from the start of m-24 to the start of m-16 (two instant markers from the test log).
- Got: `Error: Invalid time value: "m-24". Expected timestamp name (ts-X), seconds (2.7), milliseconds (2700ms), or percentage (10%)`
- Workaround: `marker info m-N --json` for each start, then `zoom push 120585ms,120615ms`.
