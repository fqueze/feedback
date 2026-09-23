# profiler-cli feedback (test_mdn-compatibility-live-data.js)

## Question: "what was the machine's CPU use while this test ran?" (resource-usage profile)

- Command: `profiler-cli zoom push m-1 --session S; profiler-cli thread markers --session S --search "CPU Use"` and the same with `--list`.
- Expected: some summary of the `CPU Use` markers' payload (cpuPercent mean / median / share of time near 100%) over the zoomed range, or at least the CPU percent per row in `--list`.
- Got: duration and rate stats only (every marker is ~100 ms), and `--list` rows with no payload value.
- Workaround: `thread markers --list --limit 0 --json` for handles, then `marker info <all handles> --json`, then a Python mean over `fields[cpuPercent]` (script in `cpu_stats.sh` next to this file). Three commands and a script, per range. Ideas: numeric field stats in the aggregated view (`--group-by` already exists; a `--stats field:cpuPercent` would do), or print the payload's main field in `--list` rows.

## Question: "how many tests were running concurrently with this one?" (resource-usage profile)

- Command: `zoom push m-1` then `thread markers --search name:test --list --limit 0`.
- Expected: something that says how many `test` interval markers overlap a given one, over time.
- Got: the list of overlapping markers, which I had to count by hand; no concurrency over time.
- Workaround: script over `--list --json` (`concurrency.sh` next to this file). Not a big deal, but it is the first question for any xpcshell parallel-phase timeout.

## `thread samples` excluded 98% of samples as idle, without saying what idle means

- Command: `profiler-cli thread samples --session <per-test profile of a CPU-starved xpcshell test>` (profile_test_mdn-compatibility-live-data.js.json from task ZwneadX7R9S7-LR3jNAeow).
- Expected: the main thread's work. The thread is inside one 29.7 s `Jank` marker, and with `--include-idle` all 2767 samples are in JavaScript / DOM categories, deep in the test's JS.
- Got: "45 running samples", with "idle excluded" and no indication of how 2722 samples came to be idle (presumably zero thread CPU delta, since the machine was at 100% CPU and the process got ~25% of a core).
- Workaround: `--include-idle`. Suggestion: say how many samples were excluded and by which criterion ("2722 samples with no CPU time excluded"), since for a starved thread those samples are the story, not idle time.

## Reviewer (review-test_mdn-compatibility-live-data.js): the same two questions again

- The reviewer hit both questions above ("CPU use while this test ran", "how many tests ran concurrently with it") on three resource-usage profiles. Each needed `thread markers --list --limit 0 --json` plus a Python script per range (`review-*.json` next to this file). These are the first two questions for any xpcshell timeout, and they get asked again for every profile checked.
