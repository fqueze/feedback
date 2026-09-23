## `thread markers --list` times are whole seconds on a long profile, even when zoomed

- Command: `profiler-cli zoom push 592.2,593.21 --session S; profiler-cli thread markers --session S --search "TabGroup,TabClose,..." --list --limit 0`
- Expected: millisecond times in the `t=` column. The zoom was 1 s wide, and the question was the order of events tens of ms apart: TabClose, then SSWindowClosing 67 ms later.
- Got: every row read `t=9m52s` (the profile is 9m56s long), so ordering inside that second had to be inferred from row order.
- Workaround: looped `profiler-cli marker info m-N --json` over each handle and read `.start`, which cost about 30 calls.

## Short comma-OR search terms match inside unrelated runnable names

- Command: `thread markers --search "TabGroup,...,Test,..." --list`
- Expected: `Test` would add the Test-category log markers.
- Got: hundreds of `AsyncExecuteStatements::notify...` Runnables, because "Execu**teSt**atements" contains "test".
- Workaround: `--category Test` in a separate call, or `name:` terms. This is documented as a substring match, but it is easy to fall into; a whole-word or a category-only shortcut would help.
