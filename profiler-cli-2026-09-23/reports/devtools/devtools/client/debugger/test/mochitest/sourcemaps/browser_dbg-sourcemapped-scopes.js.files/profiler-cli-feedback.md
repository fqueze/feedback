## Ordering markers a few ms apart late in a profile (browser_dbg-sourcemapped-scopes.js)

- Question: in which order did the last RefreshDriverTick paint, the first "RefreshDriverTick waiting for paint", and the harness's "checking for open popups" happen (all within ~50 ms around t=236.2 s)?
- Command: `profiler-cli thread markers --session <s> --search "name:RefreshDriverTick,name:ForwardDPTransaction,..." --list --limit 0`
- Expected: timestamps precise enough to order them (e.g. `t=236.213s`).
- Got: every marker after 1 minute prints as `t=3m56s` — one-second resolution, so a hundred markers share the same time.
- Workaround: `--json` and a python one-liner printing `start/1000` with 3 decimals.
- What the output could have shown: millisecond precision for times over a minute (`t=3m56.213s`), at least in `--list` mode.

## "Was the machine saturated between t1 and t2" on a resource-usage profile

- Command: `profiler-cli counter list` / `counter info c-0` on `profile_resource-usage.json` (zoomed to 250,262).
- Expected: a CPU track summary for the zoomed range.
- Got: "No counters in this profile." The CPU data is in 100 ms `CPU Use` markers only; `thread markers` summarises their count, not their values.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a script averaging `data.cpuPercent` per second.
- What the output could have shown: min/avg/max of `CPU Percent` over the view (e.g. in `thread markers` for CPU-typed markers, or in `profile info`).
