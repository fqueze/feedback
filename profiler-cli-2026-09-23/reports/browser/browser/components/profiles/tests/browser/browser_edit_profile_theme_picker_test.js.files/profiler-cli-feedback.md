
## `marker info` does not show the marker index

- Command: `profiler-cli marker info m-46 --session <s>` (checking that a report's link `marker=329654` points at the marker it quotes).
- Expected: the default output to show the marker index, the number a profiler.firefox.com `marker=` link uses.
- Got: handle, name, times and fields only. `markerIndex` is only in `--json`, so checking 14 links needed a loop through python.
- Workaround: `marker info <m> --json | python3 -c ...`. Printing `Index: N` by default, or supporting `marker info --index N`, would answer this directly.

## `--session` before the subcommand is rejected

- Command: `profiler-cli --session <s> zoom push 22.170,22.215`
- Expected: a global option accepted in either position.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.
