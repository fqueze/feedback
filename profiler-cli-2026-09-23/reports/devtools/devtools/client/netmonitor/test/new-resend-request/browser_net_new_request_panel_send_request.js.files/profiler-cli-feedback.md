## Question: how far apart did the socket thread's status events reach the main thread?

- Command: `profiler-cli thread markers --search 'ObserveActivityWithArgs,nsTransportStatusEvent' --list --limit 0 --session <s>` inside a 30 ms zoom.
- Expected: start times precise enough to see bursts 0.4 ms vs 3 ms apart.
- Got: `t=3.787s` on every row (1 ms resolution), so two bursts 0.4 ms apart look identical to one burst; needed `--json` and a script to print `start` in ms with µs precision.
- Could have shown: more digits in `t=` when the view (zoom) is short, or a relative-to-previous-row delta column.
- Also: the Runnable name (the thing grouped on) is only in the description column, so `--group-by field:name` works but the list rows need the long description to be read.
