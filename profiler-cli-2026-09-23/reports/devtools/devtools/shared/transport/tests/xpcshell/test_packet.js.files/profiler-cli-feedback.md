## Which tests were running at time t (resource-usage profile)

- Question: "which `test` interval markers overlap t=54.0–54.1 s", and "how many tests were in progress over time". This is how you find the tests running when a job-wide failure started.
- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0` shows start and duration, but not which markers overlap a point. `zoom push 53.9,54.1` lists markers overlapping the range, but mixed with thousands of INFO markers. With `--search name:test` it works, but it does not give a concurrency count.
- Workaround: `--json`, plus a python script computing overlap and concurrency per second.
- What would have answered it: `thread markers --search name:test --at <t>` (the markers active at t), or a concurrency sparkline for interval markers of one name.
## Machine CPU use over time, in a resource-usage profile (review)

- Question: "what was the machine's CPU use per 5 s bucket between 40 and 105 s?"
- Command: `profiler-cli counter list --session review-test_packet.js-1` prints "No counters in this profile"; the CPU use is only in ~1,160 `CPU Use` interval markers (`cpuPercent` field).
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json`, plus a python script bucketing `cpuPercent`.
- What would have answered it: `counter list`/`counter info` exposing the resource-usage `CPU Use` markers as a counter (with its "over time" buckets), or `thread markers --group-by` able to bucket by time and average a numeric field.

## How many markers of a kind per second (review)

- Question: "how many `will retry` INFO markers per second, and do the 1,461 `not killing` markers line up with the 45 s TIMEOUTs?"
- Command: `profiler-cli thread markers --search "will retry" --session <s>` gives the total, not its distribution over time.
- Workaround: `--list --limit 0 --json` and a python histogram.
- What would have answered it: a per-time-bucket count (or sparkline) in the aggregated `thread markers` output.
