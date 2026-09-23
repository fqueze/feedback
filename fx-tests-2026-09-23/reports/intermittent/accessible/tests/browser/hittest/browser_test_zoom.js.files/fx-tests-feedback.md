## Which assertion fails, across a test's failing runs
- Question: across all failing runs, do the failures share the failing assertion and its point (here "Wrong direct child accessible at the point (27, 128)"), or does it vary by config?
- Command: `fx-tests test <path>` lists only the first failure per run ("Timeout while waiting for cache update", 122x). That message is the generic helper timeout, and it says nothing about which check timed out.
- Workaround: a loop of `fx-tests task <id> --messages` over 14 task IDs, parsing the output.
- Could have shown: the second failure message per run, or a `--messages` option on `fx-tests test` that aggregates every message over all failing runs with counts.
