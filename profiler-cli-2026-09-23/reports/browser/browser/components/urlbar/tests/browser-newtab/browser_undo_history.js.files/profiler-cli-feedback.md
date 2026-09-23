## Question: when, to the millisecond, did each marker in this 150 ms window happen?

- Command: `profiler-cli thread markers --category Network --list --limit 0 --session S` (also `--search NotifyObservers`, `--category Test --search <test>`), in a profile 1m18s long, zoomed to 77.80–77.95.
- Expected: start times precise enough to order markers and measure gaps (e.g. `t=77.9208s`), since the zoom is 150 ms wide.
- Got: every row printed `t=1m18s`, so the whole window reads as one instant.
- Workaround: `--json` piped through a script printing `start/1000` with 4 decimals, sorted by start.
- What the default output could show: a time precision adapted to the zoom range (or to the spread of the listed markers), e.g. `77.9208s`.

## Question: which markers are `[Urlbar]` actor messages?

- Command: `profiler-cli thread markers --list --search "Urlbar]" --session S`
- Expected: the rows displayed as `ReceiveQuery [Urlbar] StartQuery`, `SendAsyncMessage [Urlbar] InvokeContentAction`.
- Got: nothing (no output at all); `--search Urlbar` finds them. The bracketed label is composed from fields, so the displayed text is not what is searched.
- Workaround: search the bare actor name and filter.
