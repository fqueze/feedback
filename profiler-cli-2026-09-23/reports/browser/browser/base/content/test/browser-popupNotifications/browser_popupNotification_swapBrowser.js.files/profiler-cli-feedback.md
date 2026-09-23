## Question: how many sub-millisecond apart are two markers (event A vs the click that followed)?

- Command: `profiler-cli thread markers --search "...popuppositioned...,click,..." --list --limit 0 --session S` and `profiler-cli marker info m-45 m-57 m-24 m-48 m-10 m-14 --session S`
- Expected: start times precise enough to order and subtract markers that are < 1 ms apart (the failure hinges on a 0.875 ms gap between a `popuppositioned` DOMEvent and a `click`).
- Got: `--list` prints `t=3.439s` for the popuppositioned, the tryOnce runnable and the TEST-PASS, and `marker info` prints `Time: 3.439s - 3.439s (71.50μs)`: millisecond resolution only, so the gap cannot be read.
- Workaround: `marker info ... --json` / `thread markers --list --json` piped to a python script reading `start` (which is in ms with 4 decimals).
- What the output could show: an extra decimal in `t=` when markers in the listed set are within 1 ms of each other, or a `--precise`/`--relative-to m-N` option giving the offset from a reference marker.
## Question (again, from the review): how far apart are two markers less than 1 ms apart?

- Same as the entry above: reviewing meant reading `start` from `marker info ... --json` in a script, for all four profiles.
- Also: `marker info m-1 --json` returns the marker object itself, but `marker info m-1 m-2 --json` returns `{markers: [...]}`, so the same script broke when given one handle.
