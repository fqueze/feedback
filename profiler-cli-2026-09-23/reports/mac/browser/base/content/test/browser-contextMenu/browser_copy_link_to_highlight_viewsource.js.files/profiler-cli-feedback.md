## The sub-millisecond order of markers on one thread

- Question: did `UnsuppressAndInvalidate` / `PBrowser::Msg_UpdateDimensions` run before or after the synthesized `contextmenu` event, and was the load event inside the same task as the reflow — events 0.1 to 3 ms apart.
- Command: `profiler-cli thread markers --session S --list --search ...` prints `t=28.090s` for everything in that millisecond, so ordering within it can only be read from list position, which is by start time but hides the gaps.
- Workaround: `profiler-cli marker info m-.. m-.. --json | python3 -c '...start, end...'` to get the start/end in ms.
- What the default output could show: a `--precise` (or automatic when the zoom range is < 100 ms) time format such as `t=28089.568ms`, or the time relative to the zoom start.
