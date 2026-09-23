## Marker times past 60 s lose their precision in text output

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_892955 --list --limit 0` (and `marker info m-12`)
- Expected: `t=62.567s`, as for markers before 60 s (`t=56.059s`).
- Got: `t=1m3s` for every marker after 60 s, so the failure time and the last ticks all read the same, rounded to the second.
- Workaround: `profiler-cli marker info m-12 --json` and read `start` (62566.68 ms).

## Which refresh driver (window) each RefreshDriverTick belongs to, and how many ticks in a range lack a given reason

- Command: `profiler-cli thread markers --session <s> --search "name:RefreshDriverTick" --list --limit 0` (3181 rows)
- Question: does every tick after t=15.624 list `Synthetic mouse move event`, and are the ones that don't from another window's refresh driver?
- Got: one row per tick with no `innerWindowID`, so the ticks of different windows' refresh drivers look the same. Checking "all N ticks include X" means reading thousands of rows.
- Workaround: `--json`, then a Python script grouping `flatMarkers` by `data.innerWindowID` and by whether `label` contains the reason.
- Could have shown: an `innerWindowID` (or window URL) column in `--list`, or a `--group-by <field>` summary with counts per label/field value.
