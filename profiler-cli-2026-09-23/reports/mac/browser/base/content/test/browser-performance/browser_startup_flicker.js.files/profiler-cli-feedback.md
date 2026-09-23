## Question: "what changed the DOM in this time range, with the stacks"

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0` inside a `zoom push`, then `marker stack m-N` once per row.
- Expected: one command showing the few top JS frames of each listed marker's stack, since for SetNeedStyleFlush the stack is the whole payload (the list rows are blank).
- Got: a list of 16 rows with no text and a check mark each; to know which one was `Element.append` from the test I scripted `--list --json | jq .flatMarkers[].handle` into a `marker stack` loop.
- What would have answered it: a `--stacks <n>` option on `thread markers --list` printing the top n frames under each row.

