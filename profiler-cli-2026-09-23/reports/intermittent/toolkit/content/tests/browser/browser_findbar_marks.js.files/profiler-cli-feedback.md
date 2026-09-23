## Marker list timestamps lose sub-second precision past one minute

- Question: "how many ms after the `Finder:UpdateHighlightAndMatchCount` query did the iterator's `setTimeout()` runnable fire?" (a 100 ms timer vs a 5 s slip).
- Command: `profiler-cli thread markers --session browser_findbar_marks.js-1 --search "UpdateHighlightAndMatchCount,find-scrollmarks-changed,setTimeout" --list --limit 0` (also `marker info m-260 m-265 ...`).
- Expected: start times with ms precision, e.g. `t=80.6132s`.
- Got: `t=1m16s` / `t=1m21s` for every marker past 60 s, both in `--list` and in `marker info` (`Time: 1m16s (instant)`), even inside a 6 s `zoom push`. Ordering was visible, gaps were not.
- Workaround: `--json` and a python one-liner printing `start/1000` with 4 decimals. The default output could have shown e.g. `t=1m16.600s`, or seconds with 3 decimals when zoomed into a range of a few seconds.
