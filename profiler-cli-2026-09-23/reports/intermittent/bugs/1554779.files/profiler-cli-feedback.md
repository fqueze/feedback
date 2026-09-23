## Question: what was the machine's CPU use during one part of a resource-usage profile (median, p95), not just the phase average?

- Session: review-1554779 (resource-usage profile of task TMQTFgp9Q5mxsVFbQA6Ndg)
- Command: `profiler-cli counter list` gives "No counters in this profile"; `thread markers --search 'name:CPU Use'`
  aggregates only the markers' durations (all ~100 ms), not their `cpuPercent` payload. The only average is the
  `Phase` marker's (51.2 % over 50 min).
- Workaround: `thread markers --search 'name:CPU Use' --list --limit 0 --json` (44 MB for 31k markers), then a
  script to bucket `data.cpuPercent` by time: median 32 %, p95 93 % during `TextInputDelegateTest`.
- What would have answered it: the resource-usage CPU markers exposed as a counter, so `zoom push` + `counter info`
  gives the distribution; or the aggregate view of `thread markers` summarizing a numeric payload field.
