## Millisecond start of a marker, to compare two markers 36 ms apart

- Command: `profiler-cli thread markers --search test_framebindings-06 --list --limit 0 --session test_framebindings-06.js-1` and `profiler-cli marker info m-1 m-3 --session test_framebindings-06.js-1` (3m37s resource-usage profile of LKys8j8iTQKTAN9hKv040g).
- Expected: start times precise enough to tell that the test's "will retry" INFO came 36 ms after the start of its `test` marker.
- Got: `t=2m13s` for both in the list, and `Time: 2m13s - 2m58s (45.034s)` / `Time: 2m13s (instant)` in `marker info`: second resolution once the profile is over a minute long.
- Workaround: `--json` and read `start` (133172.908 vs 133208.908).
- Could have shown: `t=2m13.173s` (ms precision) in `--list` and `marker info`.

## How many tests of each status started before / after a timestamp

- Question: after the first launch failure at t=131.676s, did any test still pass, and in what window did the TIMEOUT tests start?
- Command: `profiler-cli thread markers --category Tasks --list --limit 0 --json --session test_framebindings-06.js-1`, then a Python script splitting `test` markers by `start` and counting the status prefix of `label`.
- Could have shown: `thread markers --search name:test --group-by field:status` restricted by `zoom push <t>,end` — but grouping by a payload field of the `test` marker (Status) and a zoom that keeps only markers *starting* in the range (the default keeps overlapping intervals) would answer it without a script.
