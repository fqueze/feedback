## Marker list times are rounded to the second in long profiles

- Question: in which order, and how far apart, do the nested `DocAccessible::PruneOrInsertSubtree` markers of one 18 s runnable start (to count the top-level walks and see their nesting)?
- Command: `profiler-cli thread markers --session <s> --search DocAccessible::PruneOrInsertSubtree --min-duration 100 --list --limit 0`
- Expected: start times precise enough to order markers that are milliseconds apart.
- Got: every row reads `t=6m58s` / `t=6m59s` in a 7-minute profile, so the 58 rows cannot be told apart or grouped into walks.
- Workaround: `--json` and a Python script printing `start` / `duration` in ms. A `--time-format ms` (or printing seconds with 3 decimals once the profile exceeds a minute) would have answered it.

## `thread samples-top-down` has no depth limit

- Command: `profiler-cli thread samples-top-down --session <s> --max-depth 40`
- Expected: a depth-limited tree. Got: `error: unknown option '--max-depth'`.
- Workaround: ran without it and piped to `head`; the tree was dominated by deeply inlined profiler frames, so most of the output was noise.
