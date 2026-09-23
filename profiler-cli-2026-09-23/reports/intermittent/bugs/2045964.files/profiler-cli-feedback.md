# profiler-cli feedback (bug 2045964)

## A comma inside a literal log line silently turns the search into an OR
- Command: `profiler-cli thread markers --session 2045964-1 --search "Parent 9640, Main Thread] ###!!! ASSERTION" --list --limit 1`
- Expected: the first eBlur assertion printed by parent 9640.
- Got: `m-15`, the assertion from parent **5428**. The comma split the query into `Parent 9640` OR ` Main Thread] ###!!! ASSERTION`. The guide documents this, but Gecko log lines almost always contain `[Parent N, Main Thread]`, so it is easy to fall into and nothing warns you.
- Workaround: `--search "Parent 9640"`, then grep the list output.
- Suggestion: a way to quote or escape a comma, or a note in the output that the query was split into N terms.
