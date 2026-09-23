## `thread markers --list` times are rounded to the second, even when zoomed

- Command: `profiler-cli zoom push 164.5,167.4 --session …` then `profiler-cli thread markers --session … --list --limit 0`
- Expected: millisecond start times, at least when the view spans a few seconds (e.g. `t=166.852`).
- Got: `t=2m45s`, `t=2m46s`, `t=2m47s` for all ~200 markers. The order between a `get_process_list` and the failure message 1 ms later, which was the whole question, was unreadable.
- Workaround: `--json` and print `start/1000` with a script.

## Question: "which process names did each `ps` list, over time?"

- Command: `profiler-cli thread markers --session … --search get_process_list --list --limit 0 --json`, parsed with a Python script (`ast.literal_eval` of the message), then scripts to follow each pid's name changes and each slot's occupancy (`renames.py`, `ctx.py` here).
- Default output cuts the message after the first ~20 entries (kernel threads), so the app processes at the end of each listing never show.
- What it could have shown: a way to show the part of a long payload that matches `--search` (a match-centred excerpt) instead of its start. `--search test_runner` would then have shown the `…test_runner:xpcshellN` entries of each listing.

## Question: "which marker does this link's `marker=N` select?" (review)

- Command: `profiler-cli load '<link with marker=9398>' --session …`
- Expected: the load to name the marker the link selects (handle, name, start), since `marker=N` is an index and not a handle.
- Got: only `Selected thread: t-0`. To check 30+ links I had to find each marker by `--search`/zoom and then loop `marker info <m-…> --json` one handle at a time to read `markerIndex`.
- What it could have shown: the selected marker on load, or `marker info --index N`.
