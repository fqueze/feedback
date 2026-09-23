## Question: "which refresh observer is keeping the refresh driver ticking, and since when?"
- Commands: `thread markers --search RefreshDriverTick --group-by field:name`, then `--search "Synthetic mouse move" --list --limit 0 | rg ...` with awk to find the first tick after a time
- What would have answered it: a filter by time on `--list` without a zoom push (for example `--after <t>`), or a run-length summary of consecutive markers that have the same `name` field ("from 16.592 s to the end: 1,360 ticks with the same reason").
