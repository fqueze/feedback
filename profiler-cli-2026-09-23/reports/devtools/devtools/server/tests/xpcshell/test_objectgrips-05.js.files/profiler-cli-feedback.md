## Question: "what was the machine's CPU use, second by second, over this range?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search 'name:CPU Use' --list --limit 0` (after `zoom push 125,140`)
- Expected: each CPU Use marker's CPU %/idle % on its row, or a compact time series.
- Got: rows with handle, time and duration only; the percentages are only in `marker info` (one per marker) or `--json` `.data`.
- Workaround: `--json` piped through a python script printing `start` and `data.cpuPercent`/`idle_pct`.
- Could have shown: the marker's payload summary (cpuPercent, idle_pct) in the list's description column, as it does for test markers. The profile has no counters (`counter list` is empty), so there is no other view.

## Marker handles only exist once a listing has printed them

- Command: `profiler-cli thread markers --session <s> --search 'objectgrips-05,...' --list --limit 3`, then `python3 profile-link.py --session <s> --marker m-1920` (m-1920 was the handle an earlier daemon had given the same marker, from a `--limit 0` listing).
- Expected: the link, or handles stable for the same profile.
- Got: `profiler-cli marker info m-1920: Error: Unknown marker m-1920`, because the `--limit 3` listing had not reached it.
- Workaround: rerun the full `--list --limit 0` query before linking.
- Could have shown: a hint in the error ("handles are assigned by listings in this daemon; list it first"), or handles that are stable per profile like f-N.
