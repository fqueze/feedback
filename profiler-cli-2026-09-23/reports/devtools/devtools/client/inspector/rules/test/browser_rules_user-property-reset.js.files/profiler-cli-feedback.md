## Question: "how busy was the machine between t1 and t2?" in a resource-usage profile

- Command: `profiler-cli zoom push 1984,2020 --session S; profiler-cli thread markers --search "name:CPU Use" --session S`
- Expected: some summary of the `cpuPercent` payload over the zoomed range (avg/max, or per-second buckets).
- Got: only the markers' duration stats (min/avg/max ~100 ms), which say nothing about CPU. The resource-usage
  profile has no counters, so `counter info` cannot answer either.
- Workaround: `--list --limit 0 --json` and a Python script bucketing `data.cpuPercent` by second.
  Aggregating numeric payload fields (like `--group-by` does for strings) would have answered it.

## `marker info m-a m-b --json`: records carry no `handle`

- Command: `profiler-cli marker info m-308 m-309 m-311 --session S --json`
- Expected: each record to say which handle it is for.
- Got: `handle` missing (None) in every record, so matching records to the handles asked for relies on order.
- Workaround: print a distinguishing field (URL, serial) next to each record.

## pid in marker labels is formatted with a thousands separator, and comma is the `--search` OR separator

- Command: `profiler-cli thread markers --search "Parent 2,156" --list --limit 0 --session S`
- Expected: the markers whose label reads `[Parent 2,156: Main Thread]`, as printed.
- Got: the OR of "Parent 2" and "156", with no warning, so unrelated markers were mixed in.
- Workaround: search the raw number (`2156`) or a payload field. Printing pids without a thousands separator would avoid it.
