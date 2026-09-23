## Question: was the machine's disk saturated while this test ran?

- Command: `profiler-cli profile info` and `profiler-cli counter list` on a resource-usage profile (`.../SRZHFKdBQAOzvlUQCwjAwQ/runs/0/artifacts/public/test_info/profile_resource-usage.json`).
- Expected: the IO track (bytes written/read per interval) summarized over time, like `counter info` does for counters, so a disk-throughput plateau shows at a glance.
- Got: `profile info` says "CPU activity over time: No significant activity", `counter list` says "No counters in this profile". The data is in ~12,500 `IO` interval markers (100 ms each, `write_bytes` field); `marker info` on 20 of them one by one was the only non-script view.
- Workaround: `thread markers --search name:IO --list --limit 0 --json` and a Python script bucketing `write_bytes` per 5 s. It showed writes pinned at 168.7 MB/s for 10-20 s and 45-55 s of the job, and ~170 MB/s while the failing test ran — a throttled-disk plateau.
- What would have answered it: `thread markers --search name:IO --sum write_bytes --bucket 5s`, or exposing the resource monitor's CPU/IO/Memory markers as counters so `counter info` gives the over-time table.

## (review) Question: CPU % and bytes written per 100 ms over a 1 s window of a resource-usage profile

- Command: `profiler-cli thread markers --search "name:IO,name:CPU Use,-name:NetIO" --list --limit 0 --session …` after `zoom push 36.05,37.10`.
- Expected: each row showing its main value (`cpuPercent`, `iowait_pct`, bytes written), the way Text markers show their text.
- Got: rows with only name, time and duration, and an empty description. The values are shown only by `marker info`.
- Workaround: `marker info <all handles> --json` and a Python script to tabulate them, once per resource-usage profile (3 profiles). This is the same gap as the entry above, and the reviewer hit it again, for CPU Use as well as IO.
