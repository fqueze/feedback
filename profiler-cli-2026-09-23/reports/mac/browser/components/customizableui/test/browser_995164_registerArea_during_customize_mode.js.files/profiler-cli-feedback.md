## Question: how many milliseconds apart are these markers, late in a long profile?

- Command: `profiler-cli thread markers --category Test --search browser_995164 --list --limit 0` (and the same with `--search DOMEvent` inside a 7 ms zoom)
- Expected: a time column precise enough to order and space markers that are milliseconds apart (for example `t=147.7921s`).
- Got: every row printed `t=2m28s`, the whole test (230 ms) and even a 7 ms zoom collapsing to one value, so the order of mousedown/dragstart/drop across three drags and the gap between a drag and the preceding refresh tick could not be read.
- Workaround: `marker info m-a m-b ... --json` and `thread markers ... --json` piped into a Python script printing `start` in ms.
- What the output could have shown: a time format whose precision follows the zoom/list span (ms once the span is under a minute, or always 3 decimals of seconds).

## Question: what are the refresh driver's tick times in a range?

- Command: `profiler-cli thread markers --search "RefreshDriverTick,..." --list --limit 0 --json`
- Expected: `end`/duration in the flat JSON so tick intervals can be read.
- Got: the flat list's JSON has `start` but the interval end came back as 0 duration in my script (it is only in `marker info --json` as `end`), so I had to call `marker info` per handle.
- Workaround: `marker info m-529 m-536 m-526 --json`.
