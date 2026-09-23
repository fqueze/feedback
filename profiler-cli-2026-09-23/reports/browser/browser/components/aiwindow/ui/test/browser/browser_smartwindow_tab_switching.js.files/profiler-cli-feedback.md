## Question: what happened in the parent between 12m7s and 12m12s? (review)

- Command: `profiler-cli zoom push --session <id> 12m7s,12m12s`
- Expected: a 5 s range from 727 s to 732 s. The marker list prints times as `t=12m7s`, so that is the format one copies back.
- Got: `Pushed view range: ts-1 (12s) to ts-1 (12s) (duration: 0s)` with no error, then an empty marker list for the range.
- Workaround: convert by hand to seconds (`727,731`). `zoom push` could accept the `XmYs` format it prints, or reject input it cannot parse instead of reading `12m7s` as 12 s.

## Question: which markers in session X mention the test? (review)

- Command: `profiler-cli --session <id> thread markers --search ...`
- Expected: `--session` accepted as a global option before the subcommand, as with most CLIs.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.
