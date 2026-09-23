## Machine CPU over a zoomed range in the resource-usage profile

- Command: `profiler-cli zoom push m-1 --session …-2` then `profiler-cli thread markers --search "name:CPU Use" --list`, on `profile_resource-usage.json`
- Expected: a summary of machine CPU over the range (mean, min, max, share at 100%), as `counter info` gives for counters.
- Got: `counter list` reports "No counters in this profile". CPU is only in 428 `CPU Use` markers, each with its own `CPU Percent` field, so the default output gives no aggregate.
- Workaround: `--json` and a Python script to average `data.cpuPercent`.
- Question: "was the machine saturated while this test ran". A field-value aggregate (mean, min, max) in `thread markers --group-by`, or treating the resource monitor's CPU markers as a counter, would answer it.

## Time from each marker A to the next marker B

- Command: `thread markers --search DownloadDoneResolve --list --json` and `--search BackgroundFileSaver::NotifySaveComplete --list --json`, then a script to pair them.
- Question: "how long, in total and per occurrence, did the thread wait between event A and the following event B". A `--pair <A>,<B>` or interval-between-markers view would answer it.

## Which tests ran at the same time as this one (review)

- Command: `thread markers --search name:test --list --limit 0 --json` on `profile_resource-usage.json`, then a script to count the `test` markers overlapping the test's own marker at several points.
- Question: "how many tests, and which ones, were running while this test ran". The list gives each test's start and duration, but not the overlap. `zoom push m-<test>` followed by a list of the `test` markers that intersect the range, with a count over time, would answer it.
