## Question: "which came first, crash #8 or the first process-launch failure?" (events < 100 ms apart, after t=60s)
- Command: `profiler-cli thread markers --search "..." --list --session <s>` and `profiler-cli marker info m-63 m-8 --session <s>`
- Expected: millisecond timestamps.
- Got: `t=2m12s` for both in the list, and `Time: 2m12s (instant)` in marker info: past one minute the sub-second part is dropped, so ordering nearby events is impossible.
- Workaround: `marker info --json` / `thread markers --list --json` and a small Python script printing `start/1000`. Keeping millisecond precision (e.g. `t=2m11.676s`) in the text output would avoid the script.
