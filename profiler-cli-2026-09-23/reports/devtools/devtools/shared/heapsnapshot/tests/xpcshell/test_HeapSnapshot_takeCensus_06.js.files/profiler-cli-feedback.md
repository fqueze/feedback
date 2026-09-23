## `thread markers --list` rounds times to whole seconds past 1 minute

- Command: `profiler-cli thread markers --session <s> --search "Application ran for,Launched Test App,launch_application" --list --limit 0`
- Question: in what order did ten parallel xpcshell slots launch and exit within one second? That order is needed to work out which slot a failing test got.
- Got: `t=11m20s`, `t=11m21s`, … Every row inside a second has the same time, so the order within it is lost.
- Workaround: `--json`, then a Python script over `flatMarkers[].start`. Millisecond times in the text list (e.g. `t=11m20.095s`), or a `--precise-times` flag, would have answered it.
