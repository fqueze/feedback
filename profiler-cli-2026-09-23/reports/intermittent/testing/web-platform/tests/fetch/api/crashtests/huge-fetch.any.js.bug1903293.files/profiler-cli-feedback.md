## Question: how did the machine's memory use evolve during one test, in a resource-usage profile?

- Command: `profiler-cli zoom push 128,175 --session <s>` then `profiler-cli thread markers --search name:Memory --list --limit 0 --session <s>`
- Expected: the `Memory Used` value per row, or better a counter: the resource-usage profile's `Memory`, `CPU Use`, `IO`, `NetIO` interval markers are time series.
- Got: `profile info` / `counter list` say "No counters in this profile"; the marker list shows `m-46 Memory t=2m13s 109ms` without the value; `marker info` shows it for one marker at a time.
- Workaround: `--json` and a Python script bucketing `data.used`, `data.idle_pct`, `data.write_bytes`, `data.recv_bytes` per 100 ms / per second.
- Could show: `thread markers --list` printing the marker's main field (e.g. `Memory Used: 8.57GB`, `CPU 43.8%`), or exposing these resource-usage markers as counters so `counter info` prints its "over time" buckets.
