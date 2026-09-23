## Question: how did one value logged per test (MEMORY STAT vsizeMaxContiguous) evolve across the tests of a job?

- Command: `profiler-cli thread markers --session <s> --search "MEMORY STAT" --list --limit 0` on a resource-usage profile.
- Expected: something I could read as a per-test series.
- Got: one long line per test, with the test path at the end and the numbers buried in the text; I had to pipe it through `sed` to get `test, vsize, vsizeMaxContiguous, heapAllocated` columns.
- Not really a bug; noting that this was the single most useful signal in these profiles and it took a regex to read.

## Question: how did machine memory and CPU evolve during a stall?

- Command: `profiler-cli counter list --session <s>` on a resource-usage profile, then `thread markers --search "name:CPU Use" --list --json`.
- Expected: CPU/memory as counters, with `counter info` giving the "over time" buckets.
- Got: `No counters in this profile.`; CPU and memory are ~20,000 `CPU Use` / `Memory` interval markers each, and the text list is unreadable at that size.
- Workaround: `--json` and a python script bucketing `cpuPercent` / `used` per 10 to 20 s.
- What would have answered it: `thread markers --search "name:CPU Use" --bucket 20s`-style aggregation of a numeric field, or treating the resource-monitor markers as counters.
