## Which markers in a range have function X on their stack?

- Question: which `SetNeedStyleFlush` markers between 3.36 s and 3.39 s come from
  `_animateSidebarContainer` / `resetElements` / `Element.animate`?
- Command: `profiler-cli thread markers --session <s> --has-stack --search SetNeedStyleFlush --list`,
  then `profiler-cli marker stack m-N` once per row, in a shell loop.
- Expected: some way to filter markers by a function on their stack (like `--search` on samples),
  or a leaf JS frame shown in the `--list` row.
- Got: the rows are identical (`SetNeedStyleFlush`, no detail). You only find which one is which by
  opening every stack. There were 5 to 40 per window, over 4 windows and 2 profiles.
- Workaround: `for m in ...; do profiler-cli marker stack $m | rg -o 'Element.animate|resetElements'; done`.
