## Question: what was the machine's CPU use while this one test ran? (resource-usage profile)

- Command: `profiler-cli zoom push m-1` (the `test` marker), then `profiler-cli thread markers --search "name:CPU Use"`.
- Expected: a summary of the `cpuPercent` field over the zoomed range: min, mean, max, or a per-second sparkline.
- Got: the count and duration stats of the `CPU Use` markers, which are all 100 ms. The percentage is only visible marker by marker. `profile info` says "No significant activity" and `counter list` says "No counters", although the CPU use of the whole machine is in the profile.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json | jq` on `.data.cpuPercent`, bucketed per second with awk. Numeric payload fields could get min/mean/max in the aggregated `thread markers` view, and the resource-usage profile's CPU markers could appear in `profile info` as the CPU track.

## Question: how long did the main thread wait, doing nothing but a runnable flood, before each marker X? (review-test_DownloadCore.js.bug2019127)

- Command: `profiler-cli thread markers --search "-name:DummyEvent,-name:TaskController::AddTask,-name:Awake,-name:task,-name:Jank,-name:LongTask" --list --limit 0 --json` (11 MB), then a Python script computing, for each `DownloadPlatform::DownloadDoneResolve` runnable, the time since the end of the previous marker that was not idle-loop noise.
- Expected: a way to list the gaps in a thread's activity, given a filter for what counts as noise, with the marker that ends each gap, e.g. `thread markers --gaps --min-duration 400 --search "-name:DummyEvent"`. On macOS xpcshell the main thread never sleeps (the appshell posts about 48,000 `DummyEvent`s a second), so the idle samples and `Awake` markers cannot show these waits.
- Got: nothing that answers it directly. The waits added up to 24 s of a 30 s timeout, and that was the main finding of the review.

## Question: how many tests were running at each moment, next to the machine's CPU? (resource-usage profile)

- Command: `thread markers --search name:test --list --limit 0 --json` and `--search "name:CPU Use" ... --json`, then a script counting the overlapping `test` markers at each `CPU Use` sample, to see whether the passing retry ran alone.
- Expected: `marker info` on a `test` marker to say how many other tests overlapped it and which ones, or a concurrency counter track.
- Got: only the individual markers. The report had said the retry ran alongside test_DownloadLegacy.js's retry, which in fact ended 4 ms before it started.
