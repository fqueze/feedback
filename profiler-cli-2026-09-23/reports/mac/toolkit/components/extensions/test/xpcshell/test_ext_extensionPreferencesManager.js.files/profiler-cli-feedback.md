## How far apart are two markers, below a millisecond?

- Command: `profiler-cli thread markers --session <s> --category Test --list --limit 0` (also inside `zoom push 1.330,1.354`)
- Expected: marker times precise enough to tell the order and gap of events < 1 ms apart (the diagnosis hinged on whether a `selectSetting` call and an add-on install fell in the same millisecond).
- Got: past t=1s, times print as `t=1.342s` (1 ms resolution) even when zoomed into a 24 ms window; three markers 1.3 ms apart printed as 1.341s/1.342s/1.343s.
- Workaround: `profiler-cli marker info m-683 m-685 m-689 --json` and a script reading `.start` (1341.25 / 1341.57 / 1342.55 ms).
- What could have shown it: scale the printed precision to the zoomed range, or print ms with two decimals when the view is under ~1 s.

## The start time of several markers, sub-millisecond (follow-up to the above)

- Command: `profiler-cli marker info m-57 m-7 m-27 m-29 --session <s> --json` and later `marker info m-22 m-1 m-2 m-44 --json`
- Expected: the same JSON shape as an earlier multi-handle call in another session, which was a list of records with `.start`.
- Got: a different top-level shape (my script's `r['start']` failed with `string indices must be integers`, and `.get('start')` then printed `None`), so the precise times could not be read that way.
- Workaround: fell back to the 2-decimal `t=` of `--list` for a profile under 1 s. For the other profile, settled for 1 ms resolution.

## Which marker does a profiler link's `marker=N` open? (review)

- Command: `profiler-cli load "<profiler.firefox.com link with marker=22084&thread=0>" --session <s>`, then `thread markers --search ... --list` and `marker info m-2`
- Expected: the load, or `marker info` / `--list`, to say which marker the link's `marker=22084` points to, so a reviewer can check the link against the quoted text.
- Got: the load printed only the thread and range. Neither `--list` nor plain `marker info` shows the marker index. It is only in `marker info --json` (`markerIndex`), so a script had to map the handles to indices. That is how I found that two of the report's links opened the neighbouring marker.
- Workaround: `marker info m-1 m-2 ... --json | python3 -c '...markerIndex, start...'`.
- What could have shown it: print the `marker=N` target on load, e.g. "link marker 22084 = m-49 TEST-PASS [... : 834]", or accept `marker info #22084`.
