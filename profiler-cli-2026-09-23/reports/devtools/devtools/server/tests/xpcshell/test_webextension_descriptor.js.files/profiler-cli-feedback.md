## Question: exact (ms) time of a marker, to order events a few ms apart

- Command: `profiler-cli thread markers --session <s> --search "Detected crash,Killing background,2147009284" --list --limit 0` and `profiler-cli marker info m-1 m-3 m-8`
- Expected: start times precise enough to order a harness "will retry" 42 ms after a test start, or a child-launch failure 81 ms after a deliberate crash.
- Got: `t=2m12s` for all of them, in both the list and `marker info` (no ms).
- Workaround: `--json` and a Python script reading `start`.

## Question: how many tests started after time T, and with which status

- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0`
- Expected: a way to get "tests started in range, grouped by status" (e.g. `zoom push T,end` + `--group-by field:status` restricted to markers that *start* in the range, not overlap it).
- Got: 1113 rows to read; the zoom includes markers overlapping the range, so tests that started before T show up too.
- Workaround: `--json` dump + Python script counting by `data.status` with `start >= T`.
